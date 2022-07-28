<template>
    <v-app dark>
        <v-main>
            <v-container style="max-width: 768px">

                <!-- BOTÕES DE CONTROLE -->
                <v-card elevation="0">
                    <v-card-text class="d-flex">
                        <v-btn color="error" outlined depressed @click="subVars">- Var.</v-btn>
                        <v-btn color="success" outlined depressed @click="addVars">+ Var.</v-btn>
                    <v-spacer />
                        <v-btn color="error" outlined depressed @click="subConstraineds">- Restr.</v-btn>
                        <v-btn color="success" outlined depressed @click="addConstraineds">+ Restr.</v-btn>
                    </v-card-text>
                </v-card>

                <!-- CARD FORM PRINCIPAL -->
                <v-card elevation="0">
                    <form @submit.prevent="calc()">
                        <v-card-text>

                            <!-- FUNCÃO Z -->
                            <v-row>
                                <v-col>
                                    <v-select :items="items" v-model="form.to">
                                    </v-select>
                                </v-col>

                                <v-col>
                                    <v-text-field
                                        disabled
                                        value="Z ="
                                    ></v-text-field>
                                </v-col>

                                <v-col v-for="(i, index) in form.f" :key="index">
                                    <v-text-field
                                        type="number"
                                        step="0.01"
                                        :label="'x_' + (index + 1)"
                                        v-model.number="form.f[index]"
                                    ></v-text-field>
                                </v-col>
                            </v-row>

                            <v-row>
                                <v-col> Sujeito a: </v-col>
                            </v-row>

                            <!-- RESTRICOES -->
                                <v-row v-for="(i, index) in form.constrained.length" :key="index">
                                    <!-- RESTRICOES: X -->
                                    <v-col v-for="(j, jndex) in form.constrained[index].vars" :key="jndex">
                                        <v-text-field
                                            type="number"
                                            step="0.01"
                                            :label="'x_' + (jndex + 1)"
                                            v-model.number="form.constrained[index].vars[jndex]"
                                        ></v-text-field>
                                    </v-col>

                                    <!-- RESTRICOES: <=,>= OU == -->
                                    <v-col>
                                        <v-select
                                            :items="to"
                                            v-model="form.constrained[index].to"
                                        >
                                        </v-select>
                                    </v-col>

                                    <!-- RESTRICOES: CUSTO -->
                                    <v-col>
                                        <v-text-field
                                            type="number"
                                            step="0.1"
                                            v-model.number="form.constrained[index].cost"
                                        ></v-text-field>
                                    </v-col>
                                </v-row>
                        </v-card-text>

                        <v-card-actions>
                            <v-spacer />
                            <v-btn v-if="!calculated"
                                type="submit"
                                class="align-right"
                                color="primary"
                                depressed
                                block
                                >Calcular (tableau)</v-btn
                            >
                        </v-card-actions>
                    </form>
                </v-card>

                <div v-if="calculated">

                    <v-card class="mb-8" v-for="(x, tableau) in this.iteracoes" :key="tableau">
                        <v-card-title>{{'Iteração ' + tableau }}</v-card-title>

                        <v-card-text>
                            <v-simple-table dense>
                                <thead>
                                    <tr>
                                        <th>Base</th>
                                        <th>Cb</th>
                                        <template v-for="(i, index) in x[0].length - 3">
                                            <th :key="index">{{ 'x_' + (index + 1) }}</th>
                                        </template>
                                        <th>b</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr v-for="(lines, i) in x" :key="i">
                                        <td v-for="(li, j) in lines" :key="j">
                                            {{ li }}
                                        </td>
                                    </tr>
                                </tbody>
                            </v-simple-table>
                        </v-card-text>

                    </v-card>
                    <v-sheet v-if="!valoresInteiros">
                            <v-card-text>
                                <h3 class="mt-4">X* = {{ resultado.xOtimo }}</h3>
                            <h3 class="mt-4">Z* = {{ resultado.zOtimo }}</h3>
                            </v-card-text>
                            <v-card-actions>
                                <v-spacer></v-spacer>
                                <v-btn color="secondary" block v-if="decimal" @click="calcIntegers">Calcular valores inteiros?</v-btn>
                            </v-card-actions>
                    </v-sheet>
                    <v-sheet v-else>
                        <v-card-text>
                                <h3 class="mt-4">X* = {{ resultado.xInteger }}</h3>
                                <h3 class="mt-4">Z* = {{ resultado.zInteger }}</h3>
                            </v-card-text>
                            <v-card-actions>
                                <v-spacer></v-spacer>
                                <v-btn color="secondary" elevation="0" block disabled>Inteiros calculados</v-btn>
                            </v-card-actions>
                    </v-sheet>


                </div>
            </v-container>
        </v-main>
    </v-app>
</template>

<script>
export default {
    data() {
        return {
            calculated: false,
            valoresInteiros: false,
            decimal: false,

            items: ["MAX", "MIN"],
            to: ["<=", "=", ">="],
            linhas: [],
            iteracoes: [],

            wasMIN: 0,
            countExcessos: 0,
            indexOfExcessos: [],

            teste: 0,

            pivos: [],

            resultado: {
                zOtimo: 0,
                xOtimo: [],
                zInteger: 0,
                xInteger: [],
            },

            form: {
                f: [0.4, 0.5],
                to: "",
                constrained: [
                    {
                        vars: [0.3, 0.1],
                        to: "",
                        cost: 2.7,
                        folga: [],
                        artificial: [],
                        excesso: 0,
                    },
                    {
                        vars: [0.5, 0.5],
                        to: "",
                        cost: 6,
                        folga: [],
                        artificial: [],
                        excesso: 0,
                    },
                    {
                        vars: [0.6, 0.4],
                        to: "",
                        cost: 6,
                        folga: [],
                        artificial: [],
                        excesso: 0,
                    },
                ],
            },
        };
    },
    methods: {
        subVars() {
            if(this.form.f.length > 2){
                this.form.f.pop()

                for (let i = 0; i < this.form.constrained.length; i++) {
                    this.form.constrained[i].vars.pop()
                }
            }
        },

        addVars() {
            if(this.form.f.length < 4){
                this.form.f.push(0)

                for (let i = 0; i < this.form.constrained.length; i++) {
                    this.form.constrained[i].vars.push(0)
                }
            }
        },

        subConstraineds(){
            if(this.form.constrained.length > 2){
                this.form.constrained.pop()
            }
        },

        addConstraineds(){
            if(this.form.constrained.length <= 4){
                const newConstrained = {
                    vars: [0, 0],
                    to: "",
                    cost: 0,
                    folga: [],
                    artificial: [],
                    excesso: 0,
                }

                if (this.form.constrained[0].vars.length != 2) {
                    for (let i = 0; i < this.form.constrained[0].vars.length - 2; i++) {
                        newConstrained.vars.push(0)
                    }
                }

                this.form.constrained.push(newConstrained)
            }
        },

        calc() {
            this.calculated = true;

            //TRATANDO MINIMIZACAO
            if (this.form.to == "MIN") {
                for (let i = 0; i < this.form.f.length; i++) {
                    this.form.f[i] *= -1;
                }
                this.form.to = "MAX";
                this.wasMIN = 1
            }

            //linha Z
            let linhaZ = []

            linhaZ.push('Z')
            linhaZ.push(0)

            for (let i = 0; i < this.form.f.length; i++) {
                linhaZ.push(this.form.f[i]);
            }

            this.linhas.push(linhaZ);

            //CRIANDO LINHAS
            for (let row = 0; row < this.form.constrained.length; row++) {
                // Criando as linhas
                let linha = [];

                // Base
                linha.push('x_' + (this.form.f.length + 1 + row ))

                // Cb
                linha.push(0)


                // Variáveis
                for (let i = 0; i < this.form.constrained[row].vars.length; i++) {
                    linha.push(this.form.constrained[row].vars[i]);
                }

                this.linhas.push(linha);
            }

            // ------------------------

            // ADICIONANDO FOLGA, ARTIFICIAL E EXCESSO
            for (let i = 0; i < this.form.constrained.length; i++) {

                // ADICIONANDO FOLGA
                if (this.form.constrained[i].to == "<=") {
                    for (let j = 0; j < this.linhas.length; j++) {
                        this.linhas[j].push(0)

                        if(this.form.constrained.indexOf(this.form.constrained[i]) + 1 == j){
                            this.linhas[j][this.linhas[j].length - 1] = 1
                        }
                    }
                }

                // ADICIONANDO ARTIFICIAL
                if (this.form.constrained[i].to == "=") {
                    for (let j = 0; j < this.linhas.length; j++) {
                        this.linhas[j].push(0)

                        if(this.form.constrained.indexOf(this.form.constrained[i]) + 1 == j){
                            this.linhas[j][this.linhas[j].length - 1] = 1
                            this.linhas[j][1] = 1
                        }
                    }
                }

                // ADICIONANDO EXCESSO
                if (this.form.constrained[i].to == ">=") {
                    this.countExcessos++

                    for (let j = 0; j < this.linhas.length; j++) {
                        this.linhas[j].push(0)

                        if(this.form.constrained.indexOf(this.form.constrained[i]) + 1 == j){
                            this.linhas[j][this.linhas[j].length - 1] = 1
                            this.linhas[j][1] = 1
                        }

                        this.linhas[j].push(0)

                        if(this.form.constrained.indexOf(this.form.constrained[i]) + 1 == j){
                            this.linhas[j][this.linhas[j].length - 1] = -1
                            this.linhas[0][this.linhas[j].length - 1] = 1
                            this.indexOfExcessos.push(this.linhas[0].indexOf(this.linhas[0][this.linhas[j].length - 1]))
                            console.log('indexOfExcessos: ' + this.indexOfExcessos)
                        }
                    }
                }
            }

            // COST
            for (let i = 0; i < this.form.constrained.length; i++) {
                if(i == 0){
                    this.linhas[i].push(0);
                }

                this.linhas[i + 1].push(this.form.constrained[i].cost);
            }

            //PIVO
            this.pivos.push(0)

            for (let i = 0; i < this.linhas.length; i++) {
                if(this.linhas[i][1] == 1){
                    for (let j = 0; j < this.form.constrained[0].vars.length; j++) {
                        this.linhas[0][j + 2] = 0
                    }

                    this.linhas[0][this.linhas[0].length - 1] = 0
                }
            }

            for (let i = 0; i < this.linhas.length; i++) {
                if(this.linhas[i][1] == 1){
                    for (let j = 0; j < this.form.constrained[0].vars.length; j++) {
                        this.linhas[0][j + 2] += this.linhas[i][j + 2]
                    }

                    this.linhas[0][this.linhas[0].length - 1] += this.linhas[i][this.linhas[0].length - 1]
                }
            }

            // invertendo linha z
            for (let i = 2; i < this.form.f.length + 2; i++) {
                this.linhas[0][i] *= -1
            }

            this.linhas[0][this.linhas[0].length - 1] *= -1

            this.iteracoes.push(this.linhas);

            this.iteracao(this.iteracoes[0])
        },

        iteracao(linhaAnterior) {
            let novaLinha = [];
            novaLinha = JSON.parse(JSON.stringify(linhaAnterior));

            let iteracaoExcesso = false
            let countNovaLinha0 = 0

            for (let i = 2; i < this.form.constrained[0].vars.length + 2; i++) {
                console.log(novaLinha[0][i])
                countNovaLinha0 += novaLinha[0][i]
            }


            if(countNovaLinha0 == 0 && this.countExcessos > 0){
                iteracaoExcesso = true
            }

            console.log('iteracaoExcesso' + iteracaoExcesso)
            console.log('countNovaLinha0' + countNovaLinha0)


            let maior = -1
            let queEntra = 0;

            if (iteracaoExcesso == true) {
                this.countExcessos --

                maior = novaLinha[0][this.indexOfExcessos[0]];
                queEntra = this.indexOfExcessos[0];

                this.indexOfExcessos.shift();
                console.log(maior)
                console.log(queEntra)
            } else {


                //DEFININDO PESOS
                let linhaZpositivada = [];

                for (let i = 2; i < this.form.constrained[0].vars.length + 2; i++) {

                        linhaZpositivada.push(novaLinha[0][i] * -1)

                }

                console.log(linhaZpositivada);

                for (var i = 0; i < linhaZpositivada.length; i++) {
                    if (maior < linhaZpositivada[i] ) {
                        maior = linhaZpositivada[i];
                        queEntra = linhaZpositivada.indexOf(linhaZpositivada[i])
                    }
                }

                // queEntra += 2
            }

            console.log(maior)
            console.log(queEntra)

            //DIVISAO DOS CUSTOS PELA VARIAVEL PIVO
            let divisao = [];

            for (let i = 1; i < novaLinha.length; i++) {
                if(novaLinha[i][novaLinha[i].length - 1] == 0) {
                        divisao.push(Infinity)
                    } else {
                        if(iteracaoExcesso == false){
                            divisao.push(parseFloat(novaLinha[i][novaLinha[i].length - 1]) / linhaAnterior[i][queEntra + 2]);
                        } else {
                            divisao.push(parseFloat(novaLinha[i][novaLinha[i].length - 1]) / linhaAnterior[i][queEntra]);
                        }

                    }

                // for (let i = 0; i < divisao.length; i++) {
                //     if(divisao[i] < 0){
                //         divisao[i] = divisao[i] * -1;
                //     }
                // }
            }

            console.log(divisao)

            //QUE SAI
            var menor = 999999999;
            var queSai = 0;

            for (var i = 0; i < divisao.length; i++) {
                if (menor > divisao[i] && divisao[i] > 0) {
                    menor = divisao[i];
                    queSai = divisao.indexOf(divisao[i])
                }
            }

            console.log(menor)
            console.log(queSai)

            //AJUSTANDO AS POSIÇÕES DE ACORDO COM O TABLEAU
            if(iteracaoExcesso == false) {
                queEntra += 2
            }
            queSai += 1

            console.log('queEntraPosicionada: ' + queEntra) //2
            console.log('queSaiPosicionada' + queSai) //1

            //ITERANDO
            for (let i = 2; i < novaLinha[queSai].length; i++) {
                novaLinha[queSai][i] = linhaAnterior[queSai][i] / linhaAnterior[queSai][queEntra];
            }

            novaLinha[queSai][0] = 'x_' + (queEntra - 1)
            console.log(novaLinha)

            for (let i = 0; i < novaLinha.length; i++) {
                for (let j = 2; j < novaLinha[queSai].length; j++) {
                    if(i != queSai) {
                        novaLinha[i][j] = linhaAnterior[i][j] - (linhaAnterior[i][queEntra] * novaLinha[queSai][j]);
                    }
                }
            }

            this.iteracoes.push(novaLinha)

            // mais iterações?
            let maisiteracoes = false;

            if(countNovaLinha0 == 0 && this.countExcessos == 0) {
                maisiteracoes = false;
            } else {
                maisiteracoes = true;
            }


            // this.teste++
            if(maisiteracoes) {
                this.iteracao(this.iteracoes[this.iteracoes.length - 1])
            } else {

                // X otimo

                let counter = 1;
                for (let j = 2; j < this.form.f.length + 2; j++) {
                    for (let i = 1; i < novaLinha.length; i++) {

                        if(novaLinha[i][j] == 1) {
                            if(novaLinha[i][0].includes("x_" + counter)) {
                                this.resultado.xOtimo.push(novaLinha[i][novaLinha[i].length - 1])

                            } else {
                                this.resultado.xOtimo.push(0)
                            }
                        }
                    }
                    counter ++
                }

                this.resultado.xOtimo.forEach(i => {
                    if(this.isDecimal(i) == true ) {
                        this.decimal = true
                    }
                });
                console.log(this.resultado.xOtimo)

                // Z otimo
                for (let i = 0; i < this.form.f.length; i++) {
                    this.resultado.zOtimo += this.form.f[i] * this.resultado.xOtimo[i]
                }

                if(this.resultado.zOtimo < 0) {
                    this.resultado.zOtimo *= -1
                }

                console.log(this.resultado.zOtimo)

                console.log('nao precisa de mais iteracoes')
                console.log(this.teste)
            }
        },

        isDecimal(input){
            let regex = /^[-+]?[0-9]+\.[0-9]+$/;
            return (regex.test(input));
        },

        calcIntegers() {
            this.valoresInteiros = true

            let maioreMenorQueX = [];
            let MaioreseMenoresQueX = []

            for (let i = 0; i < this.form.f.length; i++) {

                maioreMenorQueX = [];

                    maioreMenorQueX.push(Math.floor(this.resultado.xOtimo[i]))
                    maioreMenorQueX.push(Math.ceil(this.resultado.xOtimo[i]))


                MaioreseMenoresQueX.push(maioreMenorQueX)
            }

            console.log(MaioreseMenoresQueX);

            //combinacoes possiveis
            let combinacoes = []

            combinacoes = this.combinar(MaioreseMenoresQueX)


            console.log(combinacoes)


            let resultadoInteiro = 0;
            let zInteiro = 0
            let xInteiro = []
            if(this.wasMIN){
                zInteiro = 99999
            } else {
                zInteiro = -1
            }

            for (let i = 0; i < combinacoes.length; i++) {

                if(this.wasMIN){
                    resultadoInteiro = 0
                    for (let j = 0; j < this.form.f.length; j++) {
                    resultadoInteiro += (this.form.f[j] * -1) * combinacoes[i][j]
                        console.log(resultadoInteiro)
                    }
                    if(resultadoInteiro > this.resultado.zOtimo && resultadoInteiro < zInteiro){
                        zInteiro = resultadoInteiro
                        xInteiro = [];
                        xInteiro.push(combinacoes[i])
                    }
                    } else {
                        resultadoInteiro = 0
                        for (let j = 0; j < this.form.f.length; j++) {
                            resultadoInteiro += this.form.f[j] * combinacoes[i][j]
                            console.log(resultadoInteiro)
                        }
                        if(resultadoInteiro < this.resultado.zOtimo && resultadoInteiro > zInteiro){
                            zInteiro = resultadoInteiro
                            xInteiro = [];
                            xInteiro.push(combinacoes[i])
                        }
                    }

                }

            this.resultado.zInteger = zInteiro.toFixed(2);

            for (let i = 0; i < xInteiro.length; i++) {
                this.resultado.xInteger.push(xInteiro[i])
            }
        },

        combinar(arraysToCombine) {
    var divisors = [];
    for (var i = arraysToCombine.length - 1; i >= 0; i--) {
       divisors[i] = divisors[i + 1] ? divisors[i + 1] * arraysToCombine[i + 1].length : 1;
    }

    function getPermutation(n, arraysToCombine) {
       var result = [],
           curArray;
       for (var i = 0; i < arraysToCombine.length; i++) {
          curArray = arraysToCombine[i];
          result.push(curArray[Math.floor(n / divisors[i]) % curArray.length]);
       }
       return result;
    }

    var numPerms = arraysToCombine[0].length;
    for(var i = 1; i < arraysToCombine.length; i++) {
        numPerms *= arraysToCombine[i].length;
    }

    var combinations = [];
    for(var i = 0; i < numPerms; i++) {
        combinations.push(getPermutation(i, arraysToCombine));
    }
    return combinations;
}

    },
};
</script>

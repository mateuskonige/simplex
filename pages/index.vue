<template>
    <v-app dark>
        <v-main>
            <v-container style="max-width: 768px">

                <!-- BOTÕES DE CONTROLE -->
                <v-card elevation="0">
                    <v-card-text class="d-flex">
                        <v-btn color="error" outlined depressed @click="subVars">- Variável</v-btn>
                        <v-btn color="success" outlined depressed @click="addVars">+ Variável</v-btn>
                    <v-spacer />
                        <v-btn color="error" outlined depressed @click="subConstraineds">- Restrição</v-btn>
                        <v-btn color="success" outlined depressed @click="addConstraineds">+ Restrição</v-btn>
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
                                        :label="'x_' + (index + 1)"
                                        v-model.number="form.f[index]"
                                    ></v-text-field>
                                </v-col>
                            </v-row>

                            <v-row>
                                <v-col> S.a. </v-col>
                            </v-row>

                            <!-- RESTRICOES -->
                                <v-row v-for="(i, index) in form.constrained.length" :key="index">
                                    <!-- RESTRICOES: X -->
                                    <v-col v-for="(j, jndex) in form.constrained[index].vars" :key="jndex">
                                        <v-text-field
                                            type="number"
                                            step="0.1"
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
                                >Calcular</v-btn
                            >
                        </v-card-actions>
                    </form>
                </v-card>

                <div v-if="calculated">
                    <p>Problema aumentado</p>
                    <v-card>
                        <v-card-text >
                            <v-simple-table dense>
                                <thead>
                                    <tr>
                                        <th>Base</th>
                                        <template v-for="(i, index) in this.linhas.length + this.form.f.length - 1 + countExcessos">
                                            <th :key="index">{{ 'x_' + (index + 1) }}</th>
                                        </template>
                                        <th>b</th>
                                    </tr>
                                </thead>

                                <tbody>
                                    <tr v-for="(lines, i) in linhas" :key="i">
                                        <td v-for="(li, j) in lines" :key="j">{{ li }}</td>
                                    </tr>
                                </tbody>
                            </v-simple-table>
                        </v-card-text>
                    </v-card>
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

            items: ["MAX", "MIN"],
            to: ["<=", "=", ">="],
            linhas: [],
            countExcessos: 0,

            resultado: {
                zOtimo: '',
                xOtimo: []
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
            }

            //linha Z
            let linhaZ = []

            linhaZ.push('Z')

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
                            this.linhas[j][0] = this.linhas[j][0] + '*'
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
                        }

                        this.linhas[j].push(0)

                        if(this.form.constrained.indexOf(this.form.constrained[i]) + 1 == j){
                            this.linhas[j][this.linhas[j].length - 1] = -1
                            this.linhas[0][this.linhas[j].length - 1] = 1
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
        },
    },
};
</script>

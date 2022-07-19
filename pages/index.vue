<template>
    <v-app dark>
        <v-main>
            <v-container style="max-width: 768px">
                <v-card elevation="0">
                    <form @submit.prevent="calc()">
                        <v-card-text>
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
                                <v-col>
                                    <v-text-field
                                        label="X1"
                                        v-model.number="form.f[0]"
                                    ></v-text-field>
                                </v-col>
                                <v-col>
                                    <v-text-field
                                        label="X2"
                                        v-model.number="form.f[1]"
                                    ></v-text-field>
                                </v-col>
                            </v-row>
                            <v-row>
                                <v-col> S.a. </v-col>
                            </v-row>
                            <div
                                v-for="(i, index) in form.constrained.length"
                                :key="index"
                            >
                                <v-row>
                                    <v-col>
                                        <v-text-field
                                            type="number"
                                            step="0.1"
                                            label="X1"
                                            v-model.number="
                                                form.constrained[index].vars[0]
                                            "
                                        ></v-text-field>
                                    </v-col>
                                    <v-col>
                                        <v-text-field
                                            type="number"
                                            step="0.1"
                                            label="X2"
                                            v-model.number="
                                                form.constrained[index].vars[1]
                                            "
                                        ></v-text-field>
                                    </v-col>
                                    <v-col>
                                        <v-select
                                            :items="to"
                                            v-model="form.constrained[index].to"
                                        >
                                        </v-select>
                                    </v-col>
                                    <v-col>
                                        <v-text-field
                                            type="number"
                                            step="0.1"
                                            v-model.number="
                                                form.constrained[index].const
                                            "
                                        ></v-text-field>
                                    </v-col>
                                </v-row>
                            </div>
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
                        <v-simple-table dense >
                            <thead>
                                <tr>
                                    <th>L</th>
                                    <th>x1</th>
                                    <th>x2</th>
                                    <th>x3</th>
                                    <th>x4</th>
                                    <th>x5</th>
                                    <th>x6</th>
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

                <p class="mt-4">Iteração 0</p>
                <v-card >

                        <v-simple-table dense v-if="calculated">
                            <thead>
                                <tr>
                                    <th>L</th>
                                    <th>x1</th>
                                    <th>x2</th>
                                    <th>x3</th>
                                    <th>x4</th>
                                    <th>x5</th>
                                    <th>x6</th>
                                    <th>b</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(lines, i) in linhas1" :key="i">

                                    <td v-for="(li, j) in lines" :key="j">{{ li }}</td>
                                </tr>
                            </tbody>
                        </v-simple-table>
                                                </v-card>
                <p class="mt-4">Iteração 1</p>
                <v-card >

                        <v-simple-table dense v-if="calculated">
                            <thead>
                                <tr>
                                    <th>L</th>
                                    <th>x1</th>
                                    <th>x2</th>
                                    <th>x3</th>
                                    <th>x4</th>
                                    <th>x5</th>
                                    <th>x6</th>
                                    <th>b</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(lines, i) in linhas2" :key="i">

                                    <td v-for="(li, j) in lines" :key="j">{{ li }}</td>
                                </tr>
                            </tbody>
                        </v-simple-table>
                                                </v-card>
                <p class="mt-4">Iteração 2</p>
                <v-card >

                        <v-simple-table dense v-if="calculated">
                            <thead>
                                <tr>
                                    <th>L</th>
                                    <th>x1</th>
                                    <th>x2</th>
                                    <th>x3</th>
                                    <th>x4</th>
                                    <th>x5</th>
                                    <th>x6</th>
                                    <th>b</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(lines, i) in linhas3" :key="i">

                                    <td v-for="(li, j) in lines" :key="j">{{ li }}</td>
                                </tr>
                            </tbody>
                        </v-simple-table>
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
            items: ["MAX", "MIN"],
            to: ["<=", "=", ">="],
            calculated: false,
            linhas: [],
            linhas1: [],
            linhas2: [],
            linhas3: [],

            form: {
                f: [0.4, 0.5],
                to: "",
                constrained: [
                    {
                        vars: [0.3, 0.1],
                        to: "",
                        const: 2.7,
                        folga: [],
                        artificial: [],
                        excesso: 0,
                    },
                    {
                        vars: [0.5, 0.5],
                        to: "",
                        const: 6,
                        folga: [],
                        artificial: [],
                        excesso: 0,
                    },
                    {
                        vars: [0.6, 0.4],
                        to: "",
                        const: 6,
                        folga: [],
                        artificial: [],
                        excesso: 0,
                    },
                ],
            },
        };
    },
    methods: {
        calc() {
            this.calculated = true;

            if (this.form.to == "MIN") {
                this.form.f[0] = this.form.f[0] * -1;
                this.form.f[1] = this.form.f[1] * -1;
                this.form.to = "MAX";
            }

            for (let index = 0; index < this.form.constrained.length; index++) {
                if (this.form.constrained[index].to == "<=") {
                    for (let j = 0; j < this.form.constrained.length; j++) {
                        if (index == j) {
                            this.form.constrained[index].folga.push(1);
                        } else {
                            this.form.constrained[index].folga.push(0);
                        }
                    }
                } else if (this.form.constrained[index].to == "=") {
                    for (let j = 0; j < this.form.constrained.length; j++) {
                        if (index == j) {
                            this.form.constrained[index].artificial.push(1);
                        } else {
                            this.form.constrained[index].artificial.push(0);
                        }
                    }
                } else {
                    for (let j = 0; j < this.form.constrained.length; j++) {
                        if (index == j) {
                            this.form.constrained[index].artificial.push(1);
                            this.form.constrained[index].excesso = -1;
                        } else {
                            this.form.constrained[index].artificial.push(0);
                        }
                    }
                }
            }

            for (let row = 0; row < this.form.constrained.length; row++) {
                const linha = [];

                for (
                    let i = 0;
                    i < this.form.constrained[row].vars.length;
                    i++
                ) {
                    linha.push(this.form.constrained[row].vars[i]);
                }
                for (
                    let i = 0;
                    i < this.form.constrained[row].folga.length;
                    i++
                ) {
                    linha.push(this.form.constrained[row].folga[i]);
                }
                for (
                    let i = 0;
                    i < this.form.constrained[row].artificial.length;
                    i++
                ) {
                    linha.push(this.form.constrained[row].artificial[i]);
                }

                    linha.push(this.form.constrained[row].excesso);
                    linha.push(this.form.constrained[row].const);


                this.linhas.push(linha);

            }

            const linhaZlength = this.linhas[0].length - this.form.constrained[0].vars.length

            console.log(linhaZlength)

            const linhaZ = []

            for (let i = 0; i < this.form.constrained[0].vars.length; i++) {
                linhaZ.push(this.form.f[i])
            }

            for (let i = 0; i < linhaZlength; i++) {
                if(i == (linhaZlength - 2)){
                    linhaZ.push(1)
                } else {
                    linhaZ.push(0)
                }
            }

            console.log('linha z: ' + linhaZ);

            this.linhas.unshift(linhaZ);

            this.linhas[0].unshift('Z')
            this.linhas[1].unshift('x3')
            this.linhas[2].unshift('x4')
            this.linhas[3].unshift('x5')

            this.linhas1 = JSON.parse(JSON.stringify(this.linhas));

            this.linhas1[0][1] = (this.linhas1[2][1] + this.linhas1[3][1]) * -1
            this.linhas1[0][2] = (this.linhas1[2][2] + this.linhas1[3][2]) * -1
            this.linhas1[0][7] = (this.linhas1[2][7] + this.linhas1[3][7]) * -1

            console.log(this.linhas1)

            console.log(this.linhas1[0].length);
            const linhas1linha0positiva = [];

            for (let i = 0; i < this.linhas1[0].length; i++) {
                linhas1linha0positiva.push(this.linhas1[0][i] * -1)
            }

            console.log(linhas1linha0positiva)

            let queEntra = null;

            for (let i = 0; i < 2; i++) {
                console.log(linhas1linha0positiva[i+1])
                if(linhas1linha0positiva[i+1] < linhas1linha0positiva[i]) {
                    queEntra = i;
                    console.log(queEntra);
                }
            }

            console.log(queEntra);

            var queSai = 0;

            let divisao = [];
            for (let index = 1; index < this.linhas1.length; index++) {
                divisao.push(parseFloat(this.linhas1[index][7]) / parseFloat(this.linhas1[0][queEntra]));

                for (let index = 0; index < divisao.length; index++) {
                    if(divisao[index] < 0){
                        divisao[index] = divisao[index] * -1;

                    }
                }
            }

            console.log(divisao);

            for (let index = 0; index < divisao.length; index++) {
                                if(divisao[index] < divisao[index+1]){
                                    queSai = index +1;
                                }

                            }

            console.log(queSai);

            this.linhas2 = JSON.parse(JSON.stringify(this.linhas1));

            console.log(this.linhas2)

            for (let index = 0; index < this.linhas2[queEntra].length; index++) {
                this.linhas2[queEntra][index] = (this.linhas1[queEntra][index] / this.linhas1[queEntra][queSai]);
            }

            this.linhas2[queEntra][0] = 'x1'
            console.log(this.linhas2)

            for (let index = 0; index < this.linhas2.length; index++) {
                for (let j = 1; j < this.linhas2[queEntra].length; j++) {
                    if(index != queEntra) {
                        this.linhas2[index][j] = this.linhas1[index][j] - (this.linhas1[index][queEntra] * this.linhas2[queEntra][j]);
                    }

                }

            }

            console.log(this.linhas2)

            // -------------

             const linhas2linha0positiva = [];

            for (let i = 0; i <= 2; i++) {
                if(this.linhas2[0][i] >= 0){

                    linhas2linha0positiva.push(this.linhas2[0][i])
                } else {

                    linhas2linha0positiva.push(this.linhas2[0][i] * -1)
                }
            }

            console.log('linhas2linha0positiva' + linhas2linha0positiva)

            for (let i = 0; i <= 2; i++) {
                console.log(linhas2linha0positiva[i])
                if(linhas2linha0positiva[i+1] > linhas2linha0positiva[i]) {
                    queEntra = i+1;
                }
            }

            console.log(queEntra);

            // var queSai = 0;

            divisao = [];
            for (let index = 1; index < this.linhas2.length; index++) {
                divisao.push(parseFloat(this.linhas2[index][7]) / parseFloat(this.linhas2[0][queEntra]));

                for (let index = 0; index < divisao.length; index++) {
                    if(divisao[index] < 0){
                        divisao[index] = divisao[index] * -1;

                    }
                }
            }

            console.log(divisao);

            for (let index = 0; index <= divisao.length; index++) {
                                if(divisao[index + 1] < divisao[index]){
                                    queSai = index +2;
                                }

                            }

            console.log(queSai);

            this.linhas3 = JSON.parse(JSON.stringify(this.linhas2));

            console.log(this.linhas3)

            for (let index = 0; index < this.linhas3[queSai].length; index++) {
                this.linhas3[queSai][index] = (this.linhas2[queSai][index] / this.linhas2[queSai][queEntra]);
            }

            this.linhas3[queSai][0] = 'x2'
            console.log(this.linhas3)

            for (let index = 0; index < this.linhas3.length; index++) {
                for (let j = 1; j < this.linhas3[queSai].length; j++) {
                    if(index != queSai) {
                        this.linhas3[index][j] = this.linhas2[index][j] - (this.linhas2[index][queEntra] * this.linhas3[queSai][j]);
                    }

                }

            }

            console.log(this.linhas3)
        },
    },
};
</script>

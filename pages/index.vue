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


                <p>Iteração 1</p>
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
                                    <td>{{ i }}</td>

                                    <td v-for="(li, j) in lines" :key="j">{{ li }}</td>
                                </tr>
                            </tbody>
                        </v-simple-table>


                    </v-card-text>
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
                                <tr v-for="(lines, i) in linhas1" :key="i">
                                    <td>{{ i }}</td>

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
                const linhaZ = []

            for (let i = 0; i < this.form.constrained[0].vars.length; i++) {
                linhaZ.push(this.form.f[i])
            }

            for (let i = 0; i < linhaZlength; i++) {
                linhaZ.push(0)
            }

            this.linhas.unshift(linhaZ);

            this.linhas1 = this.linhas;

            //1
            console.log(this.linhas)

            this.linhas1 = JSON.parse(JSON.stringify(this.linhas));

            this.linhas1[0][0] = (this.linhas1[2][0] + this.linhas1[3][0]) * -1
            this.linhas1[0][1] = (this.linhas1[2][1] + this.linhas1[3][1]) * -1

            //2
            console.log(this.linhas)
            //3
            console.log(this.linhas1)



            console.log(this.linhas1[0].length);
            const linhas1linha0positiva = [];

            for (let i = 0; i < this.linhas1[0].length; i++) {
                linhas1linha0positiva.push(this.linhas1[0][i] * -1)
            }

            var queEntra = 0;


            for (let i = 0; i < this.linhas1[0].length; i++) {
                if(linhas1linha0positiva[i] < linhas1linha0positiva[i+1])
                    queEntra = i;
            }

            console.log(queEntra);

            var queSai = 0;

            let divisao = [];
            for (let index = 1; index < this.linhas1.length; index++) {
                divisao.push(parseFloat(this.linhas1[index][6]) / parseFloat(this.linhas1[0][queEntra]));

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
        },
    },
};
</script>

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
                <p class="mt-4">Iteração 3</p>
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
                                <tr v-for="(lines, i) in linhas4" :key="i">

                                    <td v-for="(li, j) in lines" :key="j">{{ li }}</td>
                                </tr>
                            </tbody>
                        </v-simple-table>
                                                </v-card>
                                <p class="mt-4">X* = {{ resultado.xOtimo }}</p>

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
            linhas4: [],
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
                        const: 2.7,
                        folga: [],
                        artificial: [],
                        excesso: [],
                    },
                    {
                        vars: [0.5, 0.5],
                        to: "",
                        const: 6,
                        folga: [],
                        artificial: [],
                        excesso: [],
                    },
                    {
                        vars: [0.6, 0.4],
                        to: "",
                        const: 6,
                        folga: [],
                        artificial: [],
                        excesso: [],
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

            for (let row = 0; row < this.form.constrained.length; row++) {
                let linha = [];

                for (
                    let i = 0;
                    i < this.form.constrained[row].vars.length;
                    i++
                ) {
                    linha.push(this.form.constrained[row].vars[i]);
                }

                this.linhas.push(linha);

            }

            for (let index = 0; index < this.form.constrained.length; index++) {
                if (this.form.constrained[index].to == "<=") {

                    this.linhas[0].push(1)
                    this.linhas[1].push(0)
                    this.linhas[2].push(0)

                } else if (this.form.constrained[index].to == "=") {

                    this.linhas[0].push(0)
                    this.linhas[1].push(1)
                    this.linhas[2].push(0)

                } else {

                    this.linhas[0].push(0)
                    this.linhas[1].push(0)
                    this.linhas[2].push(-1)

                    this.linhas[0].push(0)
                    this.linhas[1].push(0)
                    this.linhas[2].push(1)
                }
            }

            for (let row = 0; row < this.form.constrained.length; row++) {
                this.linhas[row].push(this.form.constrained[row].const)
            }
        },
    },
};
</script>

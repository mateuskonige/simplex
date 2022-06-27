<template>
    <v-app dark>
        <v-main>
            <v-container>
                <v-card elevation="0">
                    <form @submit.prevent="calc()">
                        <v-card-text>
                            <v-row>
                                <v-col>
                                    <v-select :items="items" v-model.number="form.to">
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
                                            type="number" step="0.1"
                                            label="X1"
                                            v-model.number="
                                                form.constrained[index].vars[0]
                                            "
                                        ></v-text-field>
                                    </v-col>
                                    <v-col>
                                        <v-text-field
                                            type="number" step="0.1"
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
                                            type="number" step="0.1"
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
                            <v-btn
                                type="submit"
                                class="align-right"
                                color="primary"
                                depressed
                                >Calcular</v-btn
                            >
                        </v-card-actions>
                    </form>
                </v-card>
                <v-card>
                    <v-card-text>
                        <v-row>
                            <v-col>
                                {{ form.to }} Z = {{ form.f[0] }}x1
                                {{ form.f[1] }}x2 <br />
                                S.a. <br />
                                <div
                                    v-for="(i, index) in form.constrained
                                        .length"
                                    :key="index"
                                >
                                    {{ form.constrained[index].vars[0] }}x1
                                    {{ form.constrained[index].vars[1] }}x2
                                    {{ form.constrained[index].to }}
                                    {{ form.constrained[index].const }}
                                    {{ form.constrained[index].folga }}
                                    {{ form.constrained[index].artificial }}
                                    {{ form.constrained[index].excesso }}
                                </div>
                            </v-col>
                        </v-row>
                        <!-- <v-simple-table>
                            <table>
                                <thead>
                                    <tr>
                                        <th></th>
                                        <th>Z</th>
                                        <div v-for="value in z" :key="value">
                                            <th>{{ value }}</th>
                                        </div>
                                    </tr>
                                </thead>
                                <tbody>


                                </tbody>
                            </table>
                        </v-simple-table> -->
                    </v-card-text>
                </v-card>
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
            form: {
                f: [0, 0],
                to: "",
                constrained: [
                    {
                        vars: [0, 0],
                        to: "",
                        const: 0,
                        folga: [],
                        artificial: [],
                        excesso: [],
                    },
                    {
                        vars: [0, 0],
                        to: "",
                        const: 0,
                        folga: [],
                        artificial: [],
                        excesso: [],
                    },
                    {
                        vars: [0, 0],
                        to: "",
                        const: 0,
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
                            this.form.constrained[index].excesso.push(-1);
                        } else {
                            this.form.constrained[index].artificial.push(0);
                        }
                    }
                }
            }



    for (let row = 0; row < this.form.constrained.length; row++) {
        const linha = [];

                    for (let i = 0; i < this.form.constrained[row].vars.length; i++) {
                linha.push(this.form.constrained[row].vars[i]);


            }
                    for (let i = 0; i < this.form.constrained[row].folga.length; i++) {
                linha.push(this.form.constrained[row].folga[i]);


            }
                    for (let i = 0; i < this.form.constrained[row].artificial.length; i++) {
                linha.push(this.form.constrained[row].artificial[i]);


            }
                    for (let i = 0; i < this.form.constrained[row].excesso.length; i++) {
                linha.push(this.form.constrained[row].excesso[i]);


            }

            console.log(linha);


    }

        },
    },
};
</script>

<template>
    <v-app dark>
        <v-main>
            <v-container>
                <v-card elevation="0">
                    <form @submit.prevent="calc()">
                        <v-card-text>
                            <v-row>
                                <v-col>
                                    <v-select :items="items" v-model="form.to"> </v-select>
                                </v-col>
                                <v-col>
                                    <v-text-field
                                        disabled
                                        value="Z ="
                                    ></v-text-field>
                                </v-col>
                                <v-col>
                                    <v-text-field label="X1" v-model="form.f[0]"></v-text-field>
                                </v-col>
                                <v-col>
                                    <v-text-field label="X2"  v-model="form.f[1]"></v-text-field>
                                </v-col>
                            </v-row>
                            <v-row>
                                <v-col> S.a. </v-col>
                            </v-row>
                            <div v-for="(i, index) in form.constrained.length" :key="index">
                            <v-row>
                                <v-col>
                                    <v-text-field type="number" label="X1" v-model="form.constrained[index].vars[0]"></v-text-field>
                                </v-col>
                                <v-col>
                                    <v-text-field type="number" label="X2" v-model="form.constrained[index].vars[1]"></v-text-field>
                                </v-col>
                                <v-col>
                                    <v-select :items="to" v-model="form.constrained[index].to"> </v-select>
                                </v-col>
                                <v-col>
                                    <v-text-field type="number" v-model="form.constrained[index].const"></v-text-field>
                                </v-col>
                            </v-row>
                            </div>

                        </v-card-text>
                        <v-card-actions>
                            <v-spacer />
                            <v-btn type="submit" class="align-right" color="primary" depressed
                                >Calcular</v-btn
                            >
                        </v-card-actions>
                    </form>
                </v-card>
                <v-card>
                    <v-card-text>
                        <v-row>
                            <v-col>
                                {{ form.to }} Z = {{form.f[0]}}x1 {{form.f[1]}}x2 <br>
                                S.a. <br>
                               <div v-for="(i, index) in form.constrained.length" :key="index">
                                {{form.constrained[index].vars[0]}}x1 {{form.constrained[index].vars[1]}}x2 {{form.constrained[index].to}} {{form.constrained[index].const}}
                                {{form.constrained[index].folga}}
                                {{form.constrained[index].artificial}}
                                {{form.constrained[index].excesso}}
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
                to: '',
                constrained: [
                    {vars: [0,0], to: '', const: 0, folga: [], artificial: [], excesso: []},
                    {vars: [0,0], to: '', const: 0, folga: [], artificial: [], excesso: []},
                    {vars: [0,0], to: '', const: 0, folga: [], artificial: [], excesso: []},
                ],
            }
        };
    },
    methods: {
        calc(){
            for (let index = 0; index < this.form.constrained.length; index++) {
                if (this.form.constrained[index].to == '<=') {
                    this.form.constrained[index].folga.push(1);
                } else if(this.form.constrained[index].to == '=') {
                    this.form.constrained[index].artificial.push(1);
                } else {
                    this.form.constrained[index].artificial.push(1);
                    this.form.constrained[index].excesso.push(-1);
                }
            }
        }
    }
};
</script>

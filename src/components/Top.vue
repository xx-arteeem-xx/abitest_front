<script>
    export default {
        data() {
            return {
                data: {
                    message: []
                },
                data2: {
                    message: []
                },
                data3: {
                    message: []
                },
                openedId2: 0,
                openedId3: 0,
                error: "",
                loading: false
            }
        },
        methods: {
            getData(level, id) {
                try {
                    let headers = new Headers();
                    headers.append('Content-Type', 'application/json');
                    headers.append('Accept', 'application/json');
                    headers.append('Access-Control-Allow-Origin', '*');
                    headers.append('Access-Control-Allow-Credentials', 'true');
                    headers.append('GET', 'POST', 'OPTIONS');

                    fetch(`${import.meta.env.VITE_API_URL}/api/data/level/${level}/${id}`, {
                        method: 'GET',
                        headers: headers,
                    })
                        .then(response => response.json())
                        .then((data) => {
                            switch (level) {
                                case 1:
                                    this.data = data;
                                    break;
                                case 2:
                                    this.data2 = data;
                                    this.openedId2 = id;
                                    break;
                                case 3:
                                    this.data3 = data;
                                    this.openedId3 = id;
                                    break;
                                default:
                                    break;
                            }
                        })
                        .catch((error) => {
                            this.error = error;
                        });
                } catch (error) {
                    this.error = error;
                }
            },

            secondLevel(id){
                if (this.openedId2 == id) {
                    return
                };

                this.getData(2, id);
            },
            thirdLevel(id, isterminal) {
                if (isterminal == "Да") {
                    return
                };

                if (this.openedId3 == id) {
                    return
                };

                this.getData(3, id);
            }
        },
        mounted() {
            this.getData(1, 0);
        }
    }
</script>

<template>
    <div class="container my-5">
        <!-- Вывод ошибок -->
        <div v-if="error != ''">
            <div class="alert alert-danger mt-3" role="alert">
                {{ error }}
            </div>
        </div>

        <!-- || ПЕРВЫЙ УРОВЕНЬ || -->
        <div v-if="data.message" id="accordion" class="my-5 accordion">
            <div class="accordion-item" v-for="item in data.message" >
                <h2 class="accordion-header">
                    <button class="accordion-button collapsed" 
                        data-bs-toggle="collapse" 
                        :data-bs-target="'#item'+item.id" 
                        aria-expanded="false" 
                        :aria-controls="'#item'+item.id"
                        @click="secondLevel(item.id)">
                        {{ item.question }}
                    </button>
                </h2>
                <div :id="'item'+item.id" class="accordion-collapse collapse" data-bs-parent="#accordion">
                    <div class="accordion-body">

                        <!-- || ВТОРОЙ УРОВЕНЬ || -->
                        <div :id="'accordion'+item.id" class="accordion" v-if="item.id == openedId2">
                            <div class="accordion-item" v-for="elem in data2.message" >
                                <h2 class="accordion-header">
                                    <button class="accordion-button collapsed" 
                                        data-bs-toggle="collapse" 
                                        :data-bs-target="'#elem'+elem.id" 
                                        aria-expanded="false" 
                                        :aria-controls="'#elem'+elem.id"
                                        @click="thirdLevel(elem.id, elem.isterminal)">
                                        {{ elem.question }}
                                    </button>
                                </h2>
                                <div :id="'elem'+elem.id" class="accordion-collapse collapse" :data-bs-parent="'#accordion'+item.id">
                                    <div class="accordion-body">
                                        <p class="lead" v-if="elem.isterminal == 'Да'">
                                            {{ elem.answer }}
                                        </p>

                                        <!-- || ТРЕТИЙ УРОВЕНЬ || -->
                                        <div :id="'accordion'+elem.id" class="accordion" v-if="elem.isterminal == 'Нет' && elem.id == openedId3">
                                            <div class="accordion-item" v-for="e in data3.message" >
                                                <h2 class="accordion-header">
                                                    <button class="accordion-button collapsed" 
                                                        data-bs-toggle="collapse" 
                                                        :data-bs-target="'#e'+e.id" 
                                                        aria-expanded="false" 
                                                        :aria-controls="'#e'+e.id">
                                                        {{ e.question }}
                                                    </button>
                                                </h2>
                                                <div :id="'e'+e.id" class="accordion-collapse collapse" :data-bs-parent="'#accordion'+elem.id">
                                                    <div class="accordion-body">
                                                        <p class="lead">
                                                            {{ e.answer }}
                                                        </p>
                                                    </div>
                                                </div>
                                            </div>
                                        </div>

                                    </div>
                                </div>
                            </div>
                        </div>

                    </div>
                </div>
            </div>
        </div>

    </div>
</template>

<style>

</style>
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
            secondLevel(id){
                if (this.openedId2 == id) {
                    return
                };

                try {
                    fetch(`http://localhost:5000/api/data/level/2/${id}`)
                        .then(response => response.json())
                        .then((data) => {
                            this.data2 = data;
                            this.openedId2 = id;
                        })
                        .catch((error) => {
                            this.error = error;
                        });
                } catch (error) {
                    this.error = error;
                }
            },
            thirdLevel(id, isterminal) {
                if (isterminal == "Да") {
                    return
                };

                if (this.openedId3 == id) {
                    return
                };

                try {
                    fetch(`http://localhost:5000/api/data/level/3/${id}`)
                        .then(response => response.json())
                        .then((data) => {
                            this.data3 = data;
                            this.openedId3 = id;
                        })
                        .catch((error) => {
                            this.error = error;
                        });
                } catch (error) {
                    this.error = error;
                }
            }
        },
        mounted() {
            try {
                fetch('http://localhost:5000/api/data/level/1/0')
                    .then(response => response.json())
                    .then((data) => {
                        this.data = data;
                    })
                    .catch((error) => {
                        this.error = error;
                    });
            } catch (error) {
                this.error = error;
            }
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
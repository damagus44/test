<template>
    <div id="app">
        <v-app>
            <div class="becauseImNotDesigner"></div>
            <v-card class="table">
                <v-card-title>
                    Search of all npm
                    <v-spacer></v-spacer>
                    <v-text-field
                            v-model="search"
                            @keyup.enter.native="onEnter"
                            append-icon="mdi-magnify"
                            label="Search"
                            single-line
                            hide-details
                    ></v-text-field>
                </v-card-title>
                <v-data-table
                        :headers="headers"
                        :items="desserts"
                        @click:row="handleClick"
                        :footer-props="{
                            'items-per-page-options': [10]
                        }"
                ></v-data-table>
            </v-card>
            <div class="footer">
                <div class="container">
                    <p><a href="mailto:ask@htmlbook.ru">Damagus44@gmail.com</a></p>
                    <p><a href="https://github.com/damagus44">GitHub</a></p>
                </div>
                <p class="name">Ilin Nikolay</p>

            </div>

            <div class="text-center">
                <v-dialog
                        v-model="dialog"
                        width="500"
                >
                    <v-card>
                        <v-card-title class="headline grey lighten-2">
                            {{itemName}}
                        </v-card-title>

                        <v-card-text>
                            <p v-if="author">Author:{{author}}</p>
                            <p v-if="description">Description:{{description}}</p>
                            <p v-if="homePage">URL:{{homePage}}</p>
                        </v-card-text>

                        <v-divider></v-divider>

                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn
                                    color="primary"
                                    text
                                    @click="dialog = false"
                            >
                                OK
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </v-dialog>
            </div>
        </v-app>
    </div>
</template>

<script>
    import axios from 'axios'

    export default {
        name: 'App',
        data () {
            return {
                search: '',
                headers: [
                    {
                        text: 'Name',
                        align: 'start',
                        sortable: false,
                        value: 'name'
                    },
                    {
                        text: 'description',
                        align: 'end',
                        sortable: false,
                        value: 'description'
                    }
                ],
                resultData: [],
                dialog: false,
                itemName: '',
                description: '',
                author: '',
                homePage: ''
            }
        },
        computed: {
            desserts: function () {
                let arr = []
                this.resultData.forEach((item, index) => {
                    arr.push({
                        name: item.package.name,
                        description: item.package.description,
                        author: item.package.author ? item.package.author.name : '',
                        homePage: item.package.links ? item.package.links.homepage : ''
                    })
                })
                return arr
            }
        },
        methods: {
            handleClick: function (item) {
                this.itemName = item.name;
                this.author = item.author;
                this.homePage = item.homePage;
                this.description = item.description;
                this.dialog = !this.dialog;
            },
            onEnter: function () {
                axios({
                    method: 'get',
                    url: 'http://registry.npmjs.com/-/v1/search?text=' + this.search
                }).then((data) => {
                    this.resultData = data.data.objects
                    this.search = ''
                    // eslint-disable-next-line handle-callback-err
                }, (err) => {
                })
            }
        }
    }

</script>

<style lang="scss" scoped>
    #app{
        background-color: #a8a8a8;
    }

    .v-application{
        .becauseImNotDesigner{
            margin-top: 2%;
            width: 80%;
            height: 5%;
            margin-left: 10%;
            margin-right: 10%;
            background-color: #2c3e50;
            border-top-left-radius: 4px;
            border-top-right-radius: 4px;
        }
        .table{
            margin-left: 10%;
            margin-right: 10%;
            padding-left: 3%;
            padding-right: 3%;
            min-height: 70%;
        }
        .v-data-table .v-data-footer{
            display: flex;
            flex-direction: row;
            flex-wrap: nowrap;
            justify-content: center;
            align-items: center;
            align-content: stretch;
        }
        .footer{
            width: 80%;
            height: 15%;
            margin-left: 10%;
            margin-right: 10%;
            background-color: #2c3e50;
            border-bottom-left-radius: 4px;
            border-bottom-right-radius: 4px;
            text-align: center;
            font-size: 1rem;
            .container{
                padding-top: 20px;
                a{
                    text-decoration: none;
                    color: #ffffff;
                }
                p{
                    margin-bottom: 8px;
                }
            }
            .name{
                color: #6489ae;
            }
        }
        .v-dialog>.v-card>.v-card__text{
            padding: 20px 24px 20px;
        }
    }
</style>

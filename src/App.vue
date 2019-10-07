<template>
    <div id="app" class="container">
        <h1>Test Vue issues</h1>

        <section v-if="errored">
            <p>We're sorry, we're not able to retrieve this information at the moment, please try back later</p>
        </section>

        <section v-else>
            <div class="loading" v-if="loading">Loading...</div>
            <div v-else>
                <div
                        v-for="issue in issues"
                        class="issue"
                        :key = "issue.id"
                >
                    <div class="title">{{ issue.title }}</div>
                    <div class="donotwrap">
                        <div>comments: {{ issue.comments }}</div>
                        <div>state: {{ issue.state }}</div>
                    </div>
                </div>
                <div class="pagination">
                    <span class="pointer" v-if="page>1" @click="prevPage">Previous</span>
                    <span class="pointer" v-if="issues.length>=30" @click="nextPage">Next</span>
                </div>
            </div>

        </section>
    </div>
</template>

<script>
    import axios from "axios";

    export default {
        data() {
            return {
                issues: null,
                loading: true,
                errored: false,
                page: 1
            };
        },
        filters: {
            currencydecimal(value) {
                return value.toFixed(2);
            }
        },
        mounted() {
            this.getData();
        },
        methods:{
            nextPage(){
                this.loading = true;
                this.page++;
                this.getData()
            },
            prevPage(){
                if (this.page>1)
                    this.loading = true;
                    this.page--;
                this.getData()
            },
            getData(){
                axios
                    .get('https://api.github.com/repos/vuejs/vue/issues?state=open&page='+this.page)
                    .then(response => {
                        this.issues = response.data;
                    })
                    .catch(error => {
                        console.log(error);
                        this.errored = true;
                    })
                    .finally(() => (this.loading = false));
            }
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        color: #2c3e50;
        margin-top: 60px;
        user-select: none;
    }
    h1, h2 {
        font-weight: normal;
        text-align: center;
    }
    .pointer{
        cursor: pointer;
    }
    .loading{
        height: 70vh;
        display: flex;
        justify-content: center;
        align-items: center;
    }
    .container{
        max-width: 1200px;
        width: 94%;
        margin: auto;
    }
    .issue{
        background-color: #eee;
        border: solid 1px #ccc;
        border-radius: .3em;
        padding: 1em;
        margin-bottom: 1em;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }
    .donotwrap{
        white-space: pre;
    }
    .pagination {
        text-align: center;
    }
    .pagination>span:not(:first-of-type){
        padding-left: 2em;
    }

</style>

<!-- This example requires Tailwind CSS v2.0+ -->
<template>
    <div class="flex flex-col">

        <data-table-alert ref="dataTableAlert"></data-table-alert>
        <search></search>

        <tool-bar :list="selectedList"  ></tool-bar>


        <div class="-my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
            <div class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8">
                <div class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg">

                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                        <tr>

                            <th scope="col" class="px-6 py-3 text-left align-middle text-xs font-medium text-gray-500 uppercase tracking-wider">

                                <label class="cursor-pointer inline-flex items-center mt-3 select-none">
                                    <input @click="selectAll()" type="checkbox" id="SelectAll" class="cursor-pointer form-checkbox h-5 w-5 mr-2 text-blue-400 rounded focus:ring-0 " >
                                    Select All
                                </label>




                            </th>

                                 <!--           head of table             -->
                            <th v-if="column.show != false" v-for="(column, index) in tableColumns" scope="col" class="cursor-pointer px-6 py-3 text-left text-xs font-medium text-gray-500  tracking-wider">
                                {{ column.label }}
                                <i v-if="column.sort.sortable != false"
                                    @click="sortAble().changeSortDir({
                                'index': index,
                                'event': $event,
                                'dir': column.sort.sortDir,
                                'column': column.sort.sortColumn
                                })"
                                   class="fas "
                                   :class="sortAble().dirExists(column.sort.sortDir) == 'asc' ? 'fa-angle-up': 'fa-angle-down'"></i>
                            </th>

                            <th class="cursor-pointer px-6 py-3 text-left text-xs font-medium text-gray-500  tracking-wider">Action</th>




<!--                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">-->
<!--                                Title-->
<!--                            </th>-->

                            <th scope="col" class="relative px-6 py-3">
                                <span class="sr-only">Edit</span>
                            </th>
                        </tr>
                        </thead>

                        <tbody class="bg-white divide-y divide-gray-200">

                        <tr v-for="(model, index) in collection.data" :key="model.id">

                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                                <label class="inline-flex items-center mt-3">
                                    <input v-model="selectedList" :value="model.id" type="checkbox" class="selectInput form-checkbox h-5 w-5 text-blue-400 rounded focus:ring-0" >
                                </label>
                            </td>

                            <td v-for="(column, index) in tableColumns"  class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                                <label class="inline-flex items-center mt-3" v-html="model[column.column]">
                                </label>
                            </td>

                            <td   class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">

                                <label v-if="button[1]" v-for="button in model.action" v-html="button[0]" class="inline-flex ml-2 items-center mt-3">

                                </label>
                            </td>


<!--                            <td class="px-6 py-4 whitespace-nowrap">-->
<!--                                <div class="flex items-center">-->
<!--                                    <div class="flex-shrink-0 h-10 w-10">-->
<!--                                        <img class="h-10 w-10 rounded-full object-cover avatar shadow-lg" :src="model.img" alt="" />-->
<!--                                    </div>-->
<!--                                    <div class="ml-4">-->
<!--                                        <div class="text-sm font-medium text-gray-900">-->
<!--                                            {{ model.title}}-->
<!--                                        </div>-->
<!--                                        <div class="text-sm text-gray-500">-->
<!--                                            {{ model.email }}-->
<!--                                        </div>-->
<!--                                    </div>-->
<!--                                </div>-->
<!--                            </td>-->
<!--&lt;!&ndash;                            <td class="px-6 py-4 whitespace-nowrap">&ndash;&gt;-->
<!--&lt;!&ndash;                                <div class="text-sm text-gray-900">{{ person.title }}</div>&ndash;&gt;-->
<!--&lt;!&ndash;                                <div class="text-sm text-gray-500">{{ person.department }}</div>&ndash;&gt;-->
<!--&lt;!&ndash;                            </td>&ndash;&gt;-->
<!--                            <td class="px-6 py-4 whitespace-nowrap">-->
<!--                  <span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-green-100 text-green-800">-->
<!--                    Active-->
<!--                  </span>-->
<!--                            </td>-->
<!--                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">-->
<!--                                {{ model.role }}-->
<!--                            </td>-->
                        </tr>
                        </tbody>
                    </table>

                    <pagination :collection="collection"></pagination>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

import Pagination from "./Pagination";
import Search from "./Search";
import Alert from "./Alert";
import ToolBar from "./ToolBar";

export default {
    props: ['columns', 'config'],
    data() {
        return {
            requests: {
                search: '',
                perPage: 10,
                relation: {},
                sort:{}
            },
            collection: {},
            tableColumns: this.columns,
            sort: {
                direction: 'desc'
            },
            selectedList: [],
            selectedAll: false,
            dataAlert: this.$refs.dataTableAlert,

        }
    },
    components:{
        'pagination': Pagination,
        'search': Search,
        'data-table-alert': Alert,
        'tool-bar': ToolBar,
    },

    methods: {

        all: function(url = this.config.url, requests = this.requests){


            axios.post(url, requests)
                .then(response => {

                    // handle success
                    this.collection = response.data.collection;
                    // if(response.data.success) this.collection  = JSON.parse(response.data.collection);

                }).catch( (error) => {

                // this.errors = error.response.data.errors;

            });

        },
        sortAble: function(){

            return {

                changeSortDir: (cur) =>{

                    this.requests.sort = this.sortAble().sortHandling(cur);

                    cur.event.target.classList.toggle('rotateAngle')


                    this.all(this.config.url, this.requests)

                },

                sortHandling: (cur)=>{

                    return {
                        sortDir: this.sortAble().dirToggle(cur.index, cur.dir),
                        column: cur.column,
                    }
                },
                dirExists: (dir) =>{
                    if(typeof dir == 'undefined') dir = 'asc';
                    return dir
                },
                dirToggle: (index, dir) =>{

                    let newDir = this.sortAble().dirExists(dir);
                        newDir = newDir == 'asc' ? 'desc' : 'asc';
                    this.tableColumns[index].sort.sortDir = newDir;

                    return newDir;




                }




            }

        }
       ,
        selectAll(){
            const $selectedInputs = document.getElementsByClassName('selectInput');

            this.selectedList.length && !this.selectedAll ?  this.selectedList = [] : '';
            setTimeout(() =>  [...$selectedInputs].forEach(cur => cur.click()), 0)
            this.selectedAll = !this.selectedAll;


        },
        removeSelection:function(){
            document.getElementById('SelectAll').click();

        }

    },
    mounted() {
        this.all();

        this.dataAlert = this.$refs.dataTableAlert;

    }
}
</script>

<style>
.rotateAngle{
    transform: rotate(180deg);
    transition: all .5s ease-in-out;
}
</style>

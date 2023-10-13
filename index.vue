<!-- This example requires Tailwind CSS v2.0+ -->
<template>
    <div class="flex flex-col bg-white shadow-xl p-5 pb-20 rounded">

        <data-table-alert ref="dataTableAlert"></data-table-alert>
        <search v-if="!config.hasOwnProperty('search') || config.search.show != false"></search>

        <tool-bar :list="selectedList"></tool-bar>


        <div class="-my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
            <div class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8">
                <div class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg">

                    <table class="min-w-full divide-y divide-white-200">
                        <thead class=" text-gray-600 bg-gray-50">
                        <tr>

                                                        <!-- check All rows -->

                            <th scope="col" class="px-6 py-3 text-center align-middle text-xs font-bold  uppercase tracking-wider">

                                <label class=" cursor-pointer inline-flex items-center mt-3 select-none">
                                    <input @click="selectAll()" type="checkbox" id="SelectAll" class="cursor-pointer form-checkbox h-5 w-5 mr-2 text-blue-400 rounded focus:ring-0 " >
                                </label>

                            </th>
                                 <!-- End check All rows -->

                            <template  v-for="(column, index) in tableColumns">  
                                 <!--           head of table             -->
                            <th v-if="column.show != false" scope="col" class="cursor-pointer px-6 py-3 text-center text-xs font-bold   tracking-wider">
                                {{ column.label }}
                                <i v-if="column.sort.sortable != false"
                                    @click="sortAble().changeSortDir({
                                'index': index,
                                'event': $event,
                                'dir': column.sort.sortDir,
                                'column': column.sort.sortColumn,
                                 'type': column.sort.type || 'normal',
                                'table': column.sort.table || null,
                                'foreign_key': column.sort.foreign_key || null,
                                'primary': column.sort.primary || null,
                                })"
                                   class="fas "
                                   :class="sortAble().dirExists(column.sort.sortDir) == 'asc' ? 'fa-angle-up': 'fa-angle-down'"></i>
                            </th>
                            </template>  

                            <th class="cursor-pointer px-6 py-3 text-center text-xs font-bold   tracking-wider">Action</th>





                        </tr>
                        </thead>

                        <tbody class="bg-white divide-y divide-gray-200">

                        <tr class="even:bg-slate-50" v-for="(model, index) in collection.data" :key="model.id">

                            <!-- checkbox column -->
                            <td class="text-center  px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                                <label class="inline-flex items-center mt-3">
                                    <input v-model="selectedList" :value="model.id" type="checkbox" class="selectInput form-checkbox h-5 w-5 text-blue-400 rounded focus:ring-0" >
                                </label>
                            </td>
                            <!-- End checkbox column -->

                            <td v-for="(column, index) in tableColumns"  class="text-center px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                                <label class="inline-flex items-center mt-3" v-html="model[column.column]">
                                </label>
                            </td>

                            <td   class="text-center px-6 py-4 whitespace-nowrap text-sm text-gray-500">

                                <template v-for="button in model.action">

                                    <label v-if="button[1]"  v-html="button[0]" class="inline-flex ml-2 items-center mt-3">

                                    </label>

                                </template>
                            </td>


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

import Pagination from "./Pagination.vue";
import Search from "./Search.vue";
import Alert from "./Alert.vue";
import ToolBar from "./ToolBar.vue";

export default {
    name: 'DataTable',
    props: {
        config: {
            type: Object,
            default: function(){
                return {
                    url: '/',
                    toolbar:{
                        show: true,
                        delete: {
                            url: `/`,
                            show: true
                        }
                    },
                    search: {
                        show: true
                    }
                }
            }
        },

        perPage: {
            type: Object,
            default: function(){
                return {
                    show: true,
                    counts: [10, 25, 50, 100, 250],
                }
            }
        },

        filters:{
          type:Object,
          default: function(){
                return {
                    selection:{
                        show: true,
                        data: []

                    }
                }
            }

        },

        columns:{
            type: Array,
            default: function(){
                return []
            }
        }
},
    data() {
        return {
         
            requests: {
                search: '',
                perPage: 10,
                select: {},
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
                    this.delete();
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
                        type: cur.type,
                        table: cur.table,
                        foreign_key: cur.foreign_key,
                        primary: cur.primary,
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
         
          this.selectedList.length && !this.selectedAll ? this.selectedList = [] : '';
          this.selectedList.length && this.selectedAll ? this.selectedList = [] : [...$selectedInputs].forEach(cur => this.selectedList.push(cur.getAttribute('value')));
          this.selectedAll = !this.selectedAll;

        },
        removeSelection:function(){
            document.getElementById('SelectAll').click();

        },

        // to delete single element using ajax
        delete :function(){
            setTimeout(() =>{
                let $delete = document.querySelectorAll('.delete');
                [...$delete].forEach((cur) => {
                    cur.onclick = (event) =>{

                        // confirm 
                        if ( cur.classList.contains("confirm")){
        
                        var $confirm = confirm('Are you sure');
                    
                        if(!$confirm){
                            event.preventDefault();
                            return false;
                        }
    
                        }
                        //end confirm


                        axios.delete(cur.dataset.route).then( res => {
                            this.dataAlert.setShow().setStatus('success').setMessage( res.data.message).hide(5000);
                            this.all();
                        });
                    }
                })
            }, 500);


        },
    

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

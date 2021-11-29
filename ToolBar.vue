<template>

    <div >

        <div class="flex flex-row">
            <!--   button group   -->
            <div v-if="$parent.$props.config.toolbar && $parent.$props.config.toolbar.show != false" class="my-2 ml-2 bg-gray-100 w-38	p-2 rounded-lg flex flex-row justify-center items-center">
                <div v-if="!$parent.$props.config.toolbar.hasOwnProperty('delete') || $parent.$props.config.toolbar.delete.show != false"> <i @click="deleteElements()" class="confirm text-gray-500 far fa-trash-alt cursor-pointer hover:text-red-500 transition-colors	duration-150 "></i></div>
                <!--                    <div><i @click="edit()"  class="ml-2     text-gray-500 far fa-edit cursor-pointer hover:text-blue-500 transition-colors	duration-150 "></i></div>-->
                <!--                    <div><i  @click="exportElements()" class="ml-2     text-gray-500 fas fa-file-export cursor-pointer hover:text-green-500 transition-colors	duration-150 "></i></div>-->
            </div>


            <!--   per page selection   -->
            <div v-if="!$parent.$props.hasOwnProperty('perPage') || $parent.$props.perPage.show != false" class="my-2 ml-2 bg-gray-100 w-40		p-2 rounded-lg flex flex-row justify-center items-center">
                <div class="w-full ">Per Page </div>
                <div class=" relative w-full">
                    <select @change="rowsChange()" v-model="perPage" class="focus:ring-blue-400 focus:border-blue-400 block appearance-none w-full bg-white border border-gray-400 hover:border-gray-500 px-4 py-2 pr-8 rounded shadow leading-tight focus:outline-none focus:shadow-outline">
                        <option v-for="page in $parent.perPage.counts" :value="page">{{ page }}</option>

                    </select>

                </div>
            </div>

        </div>


        <!--   select filtration   -->
        <div v-if="this.$parent.$props.filters && this.$parent.$props.filters.selection && this.$parent.$props.filters.selection.show != false"  class="flex flex-row justify-start items-center">

            <div v-for="select in this.$parent.$props.filters.selection.data"  class="my-2 ml-2 bg-gray-100 w-48 p-2 rounded-lg flex flex-row justify-center items-center">
                <div class="w-full ">{{ select.label }} </div>
                <div class=" relative w-full">
                    <select v-model="filtration.selectVal[select.label]"

                            @change="filter().select( {
                    relation : select.relation,
                    value : filtration.selectVal[select.label],
                    }, select.label)"

                            class="focus:ring-blue-400 focus:border-blue-400 block appearance-none w-full bg-white border border-gray-400 hover:border-gray-500 px-4 py-2 pr-8 rounded shadow leading-tight focus:outline-none focus:shadow-outline">
                        <option selected  value=""> All </option>
                        <option v-for="(model, index) in select.rows" :value="model['id']"> {{ model[select.column] }} </option>

                    </select>

                </div>
            </div>

        </div>


        <!--   End select filtration   -->


    </div>

</template>

<script>


export default {
    props: ['list'],
    data: function(){
        return {
            perPage: 10,
            filtration:{
                select: {},
                selectVal : {}
            }
        }
    },

    methods:{
        deleteElements: function(){
            if(this.checkList()) return;

            this.destroy();

            this.removeAllSelection();


        },
        destroy:function(){

            axios.delete(this.$parent.$props.config.toolbar.delete.url, {data: {list: this.list}})
                .then(response => {

                    // handle success
                    this.$parent.$data.dataAlert.setShow().setStatus('success').setMessage( 'your list deleted successfully').hide(5000);
                    this.$parent.selectedList = [];
                    this.$parent.all();




                }).catch( (error) => {

                // this.errors = error.response.data.errors;

            });

        },
        edit:function(){
            if(this.checkList()) return;

        },
        exportElements:function(){
            if(this.checkList()) return;

        },
        rowsChange: function(){
            this.$parent.$data.requests.perPage = this.perPage;
            this.$parent.all();
        },
        filter: function(){
            return {
                select: (model, label)=> {
                    this.filtration.select[label] = model;
                    this.$parent.$data.requests.relations =  this.filtration.select;
                    this.$parent.all();
                }

            };
        },

        checkList: function(){
            if(!this.list.length){
                this.$parent.$data.dataAlert.setShow().setStatus('error').setMessage( 'you didn\'t Selected any element').hide(5000);
                return true;
            }
            return false;
        },
        removeAllSelection: function(){
            this.$parent.selectedAll ? this.$parent.removeSelection() : '';
        },

    }


}
</script>

<template>
    <input @input="search()"  v-model="value" class="form-input w-1/3 mx-auto mb-3 rounded-xl focus:ring-blue-400 focus:border-blue-400" type="text" :placeholder="trans('search')">
</template>

<script>

export default{

    data: function(){
        return {
            language: {
                ar: {
                    search: "البحث ...",

                },
                en: {
                    search: "Search ...",
                },
            },
            value: '',
        }
    },

    methods:{
        search: function(){
            this.$parent.$data.requests.search = this.value;
            this.$parent.all();
        }
    }
}
</script>

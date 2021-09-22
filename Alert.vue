<template>


    <!-- Link on the right -->
    <div v-if="show" class="space-x-2  p-4 rounded flex items-center  my-4 shadow-lg mx-auto max-w-2xl fixed top-2 right-2 z-10" :class="`bg-${getStatus().background}-50 text-${getStatus().text}-600`">
        <div class="">
            <svg xmlns="http://www.w3.org/2000/svg" class="fill-current w-5 pt-1" viewBox="0 0 24 24"><path d="M12 0c-6.627 0-12 5.373-12 12s5.373 12 12 12 12-5.373 12-12-5.373-12-12-12zm-1.959 17l-4.5-4.319 1.395-1.435 3.08 2.937 7.021-7.183 1.422 1.409-8.418 8.591z"/></svg>
        </div>
        <h3 class="tracking-wider flex-1" :class="`text-${getStatus().text}-800`">
            {{this.message}}
        </h3>
        <button @click="show = false" class="inline-flex items-center   rounded-full p-2 hover:cursor-pointer focus:outline-none " :class="`border border-${getStatus().background}-50 hover:border-${getStatus().background}-300 hover:text-${getStatus().background}-900 hover:bg-${getStatus().background}-100`">
            <svg xmlns="http://www.w3.org/2000/svg" class="fill-current w-4 h-4 pt-1" viewBox="0 0 24 24"><path d="M24 20.188l-8.315-8.209 8.2-8.282-3.697-3.697-8.212 8.318-8.31-8.203-3.666 3.666 8.321 8.24-8.206 8.313 3.666 3.666 8.237-8.318 8.285 8.203z"/></svg>
        </button>
    </div>

</template>

<script>
export default {

    data: function(){
        return {
            message: '',
            baseStatus: 'success',
            status: {
                error: {

                    background: 'red',
                    text: 'red'

                },
                success: {

                    background: 'green',
                    text: 'green'
                },
                warning: {

                    background: 'yellow',
                    text: 'yellow'

                },
            },
            show: false,

        }
    },
    methods:{
      hide: function(time = 3000){

          setTimeout(() => this.show = false, time);

          return this;
      }  ,
        setShow: function(){
          this.show = true;

          return this;
        },
        setMessage: function (message = ''){

          this.message = message;

          return this;

        },
        setStatus: function(status){

          this.baseStatus = status;

          return this;
        },
        getStatus: function(){

          return this.status[this.baseStatus];
        }
    },
    mounted() {

    }
}
</script>

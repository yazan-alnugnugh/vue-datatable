<!-- This example requires Tailwind CSS v2.0+ -->
<template>
    <div class="bg-white px-4 py-3 flex items-center justify-between border-t border-gray-200 sm:px-6">
        <div class="flex-1 flex justify-between sm:hidden">
            <a href="#" class="relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50">
                Previous
            </a>
            <a href="#" class="ml-3 relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50">
                Next
            </a>
        </div>
        <div class="hidden sm:flex-1 sm:flex sm:items-center sm:justify-between">
            <div>
                <p class="text-sm text-gray-700">
                    Showing
                    {{ ' ' }}
                    <span class="font-medium">{{ collection.from }}</span>
                    {{ ' ' }}
                    to
                    {{ ' ' }}
                    <span class="font-medium">{{ collection.to }}</span>
                    {{ ' ' }}
                    of
                    {{ ' ' }}
                    <span class="font-medium">{{ collection.total }}</span>
                    {{ ' ' }}
                    results
                </p>
            </div>
            <div v-if="collection.total > collection.per_page">
                <nav class="relative z-0 inline-flex rounded-md shadow-sm -space-x-px" aria-label="Pagination">

                    <!-- Current: "z-10 bg-indigo-50 border-indigo-500 text-indigo-600", Default: "bg-white border-gray-300 text-gray-500 hover:bg-gray-50" -->
                    <a
                        v-for="(link, index) in collection.links"
                        aria-current="page"
                        class=" relative inline-flex items-center px-4 py-2 border text-sm font-medium"
                        :class="[link.active ? 'z-10 bg-indigo-50 border-indigo-500 text-indigo-600' : 'bg-white border-gray-300 text-gray-500 hover:bg-gray-50', {'cursor-pointer': link.url}]"
                        @click.prevent="current(link)"
                        v-html="link.label"
                    >
                    </a>

                </nav>
            </div>
        </div>
    </div>
</template>

<script>
// import { ChevronLeftIcon, ChevronRightIcon } from '@heroicons/vue/solid'

export default {

    props: ['collection'],
    methods: {
        current: function(link){


           !link.active ?  this.$parent.all(link.url) : '';

        },
        next: function(){

        },
        previous: function(){

        },
    }

}
</script>

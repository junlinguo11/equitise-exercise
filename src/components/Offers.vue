<template>
  <section class="section offers">
    <div class="container">
      <h1 class="title has-text-centered">Find offers</h1>
        <tabs>
            <tab name="Live" attr="liveOffers">
                <search-panel></search-panel>
            </tab>
            <tab name="Coming Soon" attr="comingSoon" :selected="true">
                <search-panel></search-panel>
                <div v-if="currentPage.length">
                    <div class="tile is-ancestor" v-for="i in Math.ceil(currentPage.length / 6)" :key="i">
                        <div class="tile is-parent" v-for="(offer, index) in currentPage.slice((i - 1) * 6, i * 6)" :key="index">
                            <div class="tile is-child box">
                                <p>{{offer.name}}</p>
                                <br />
                                <p>Raised Amount: {{offer.raisedAmount}}</p>
                            </div>
                        </div>
                    </div>
                </div>
                <!-- pageination -->
                <nav class="pagination is-centered" role="navigation" aria-label="pagination">
                <ul class="pagination-list">
                    <li><a class="pagination-link" v-for="(page, index) in totalPage" :key="index" @click="onClick(page.pageNumber)" :class="{'is-current': page.isActive}" :aria-label="`Goto page ${page.pageNumber}`">{{page.pageNumber}}</a></li>
                </ul>
                </nav>

            </tab>
        </tabs>     
    </div>
  </section>  
</template>
<script>
import axios from 'axios';
import Tabs from './Tabs.vue';
import Tab from './Tab.vue';
import SearchPanel from './SearchPanel.vue'
import EventBus from '../eventBus';
export default {
    name: 'Offers',
    components: { Tabs, Tab, SearchPanel },
    data() {
        return {
            offers: {},
            currentTab: 'comingSoon',
            result: {},
            currentPage: {}
        }
    },
    computed: {
        totalPage: function() {
            if(this.result[this.currentTab]) {
                const length = Math.ceil((this.result[this.currentTab].length) / 12);
                const pages = [];
                let isActive = false;
                for(let i = 0; i < length; i++) {
                    if(i === 0) {
                        isActive = true;
                    } else {
                        isActive = false;
                    }
                    pages.push({
                        pageNumber: i + 1,
                        isActive: isActive
                    })
                }
                return pages;
            }
        }
    },
    methods: {
        pagination: function(pageNo, pageSize, array) {  
            var offset = (pageNo - 1) * pageSize;  
            return (offset + pageSize >= array.length) ? array.slice(offset, array.length) : array.slice(offset, offset + pageSize);  
        },
        onClick: function(page) {
            this.currentPage = this.pagination(page, 12, this.result[this.currentTab]);
            for(let i = 0; i < this.totalPage.length; i++) {
                if(this.totalPage[i].pageNumber === page) {
                    this.totalPage[i].isActive = true;
                } else {
                    this.totalPage[i].isActive = false;
                }
            }
        }
    },
    created() {
        axios.get('https://api.equitise.exchange/offer')
            .then( response => {
                this.offers = response.data;
                this.result = Object.assign({}, this.offers);
                this.currentPage = this.pagination(1, 12, this.result[this.currentTab]);   
            })
            .catch( error => console.log(error) )
    },
    mounted() {    
        EventBus.$on('selectTab', (selectedTab) => {
            this.currentTab = selectedTab;
            this.currentPage = this.pagination(1, 12, this.result[this.currentTab]);   
        });        
        EventBus.$on('search', (keyword) => {
            if(this.offers[this.currentTab] && keyword) {
                this.result[this.currentTab] = this.offers[this.currentTab].filter((offer) => {
                    return offer.name.toLowerCase().indexOf(keyword) !== -1;
                })
                this.currentPage = this.pagination(1, 12, this.result[this.currentTab]);
            } else {
                this.result = Object.assign({}, this.offers);
            }
        });        
    }
}
</script>
<style scoped>
    .offers {
        background-image: url("../assets/pattern.png");
    }

    .box {
        transition: transform .8s, box-shadow .5s;
    }

    .box:hover {
        box-shadow: 5px 5px 5px 0px rgba(0,0,0,0.4);
        transform: translate(-5px, -5px);
        cursor: pointer;
    }

    .pagination-link {
        background-color: white;
    }

    .pagination-link.is-current {
        background-color: #e4498e !important;
        border-color: #e4498e !important;
    }

    .pagination-link:not(.is-current) {
        color: #e4498e !important;;
    }
</style>

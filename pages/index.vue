<template>
  <v-container>
      <v-card elevation="0">
        <v-tabs
          v-model="tab"
          align-tabs="center"
        >

          <v-tab value="opened">Open </v-tab>
          <v-tab value="merged">Merged</v-tab>
          <v-tab value="closed">Closed</v-tab>
          <v-tab value="all">All </v-tab>
        </v-tabs>
        <v-col class="d-flex">
            <v-select :style="{'max-width':'200px'}" class='rounded-tr-0 rounded-br-0' dense hide-details outlined label="Recent Searches" :items="history"></v-select>
            <v-text-field class='rounded-tl-0 rounded-bl-0'  dense outlined v-model="searchQuery" placeholder="Search or filter results"></v-text-field>
            <v-select v-model="filter" :style="{'max-width':'200px'}" class='ml-2' dense hide-details outlined label="Update date" :items="filters" ></v-select>
        </v-col>
        <v-tabs-items v-model="tab"> 
          <v-tab-item >
            <OpenRequests />
          </v-tab-item>
          <v-tab-item >
            <MergedRequests />
          </v-tab-item>   
          <v-tab-item >
            <ClosedRequests />
          </v-tab-item>      

          <v-tab-item >
            <AllRequests />
          </v-tab-item>
        
        </v-tabs-items>


    </v-card>
  </v-container>
</template>

<script>
import AllRequests from '../components/AllRequests.vue';
import ClosedRequests from '../components/ClosedRequests.vue';
import MergedRequests from '../components/MergedRequests.vue';
import OpenRequests from '../components/OpenRequests.vue';
export default {
    name: "IndexPage",
    components: { OpenRequests, ClosedRequests, MergedRequests, AllRequests },

    data() {
        return {
            tab: 'merged',
            desc:true,
            filter:'update',
            history:[],
            searchQuery:'',
            filters:[
              'Priority','Created date','Closed date','Updated date','Milestone due date','Popularity','Label Priority','Merged date','Title'
            ]
        };
    },
}
</script>

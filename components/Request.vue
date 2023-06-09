<template>
    <v-container class="pa-0">
            <div class="d-flex justify-space-between flex-wrap"> 
                <div :style="{'max-width':'80%'}" class='d-flex' >
                    <h3>{{request.title + ' '}} <span v-if="request.task_completion_status.count > 0" :style="{'font-size':'medium'}" class="grey--text my-auto ml-2"> {{ request.task_completion_status.completed_count }} of {{ request.task_completion_status.count }} checklist items completed</span></h3> 
                </div>
                <v-flex class="d-flex justify-end  align-center"> 
                    <v-chip v-if="request.state !== 'opened' " class="lighten-5" :color="chipColors[request.state]" :style="{'color':chipColors[request.state]}">{{request.state}}</v-chip>
                    <v-avatar class="mx-1" size="20" :style="{'border': request.merge_when_pipeline_succeeds ? '1px solid red':'1px solid green',' border-radius':'50%'}">
                        <v-icon small class="mx-1" :color="request.merge_when_pipeline_succeeds ? 'red':'green'">{{ request.merge_when_pipeline_succeeds ? 'mdi-close':'mdi-check' }}</v-icon>
                    </v-avatar>
                    <v-tooltip top v-for="assignee in request.assignees" :key="assignee.id" >
                        <template v-slot:activator="{on, attrs}">
                            <v-avatar v-bind="attrs" v-on="on"  size="20" >
                                <v-img :src="assignee.avatar_url"></v-img>
                            </v-avatar> 
                        </template>
                        Assigned to {{ assignee.name }}
                    </v-tooltip>
                    <v-tooltip top v-if="request.approvals_before_merge">
                        <template v-slot:activator="{on, attrs}">
                            <div v-bind="attrs" v-on="on" class="d-flex mx-1">
                                <v-icon color="green">mdi-account-check-outline</v-icon>
                                <p class="green--text my-auto">Approved</p>
                            </div>
                        </template>
                        <span>{{ request.approvals_before_merge }} approvers</span>
                    </v-tooltip>
                    <v-tooltip top v-if="request.upvotes">
                        <template v-slot:activator="{on, attrs}">
                            <div v-bind="attrs" v-on="on" class="d-flex mx-1">
                                <v-icon>mdi-thumb-up-outline</v-icon>
                                <p class="my-auto ml-1">{{request.upvotes}}</p>
                            </div>
                        </template>
                        <span>Upvotes</span>
                    </v-tooltip>
                    <v-tooltip top>
                        <template v-slot:activator="{on, attrs}">
                            <div v-bind="attrs" v-on="on" class="d-flex">
                                <v-icon class='outlined'>mdi-forum</v-icon>
                                <p class="my-auto ml-1">{{ request.user_notes_count }} </p>
                            </div>
                        </template>
                        <span>Comments</span>
                    </v-tooltip>
                    
                </v-flex>
            </div>
            <div class="d-flex align-center justify-space-between flex-wrap">
                <div class="d-flex">
                    <p class="grey--text my-auto" :set="[time, format] = getTimeDifference(request.created_at)">{{ request.reference }} {{ '\u00B7' }} created {{ time }} {{ format }} ago by <span class="black--text">{{ request.author.name }}</span></p>
                    <v-tooltip top v-for="label in request.labels" :key="label.id" max-width="250">
                        <template v-slot:activator="{on, attrs}" v-if="label && label.name">
                            <v-flex>
                                <v-chip v-bind="attrs" v-on="on" small :class="label.description? 'pr-1':''" :color="label.color ? label.color:'green'" class="mx-1" :style="{'color':label.text_color }" >{{ !label.description ?  label.name.split('::')[0]:label.name }} <span class="white black--text ml-1 pl-1 rounded-r-xl pr-1">{{ label.description ? label.name.split('::')[1]:'' }}</span> </v-chip>
                            </v-flex>

                        </template>
                        {{ label && label.description ?  label.description:'Scoped label' }}
                    </v-tooltip>
                </div>
                <p class="my-auto" :set="[time, format] = getTimeDifference(request.updated_at)">updated {{ time }} {{ format }} ago </p>
            </div>
            <v-divider class="mt-3"></v-divider>
    </v-container>
  </template>
  
  <script>

  export default {
      name:'request',
      props:{
        request: Object
      },
      data(){
        return {
            chipColors:{
                opened:'',
                closed:'red',
                merged:'blue'
            },
            searchQuery:''
        }
      },
      methods:{
        getTimeDifference(targetTime) {
            // Convert the target time to a Date object
            const targetDate = new Date(targetTime);
            
            // Get the current time as a Date object
            const currentDate = new Date();
            
            // Calculate the time difference in milliseconds
            const timeDiffMs = currentDate.getTime() - targetDate.getTime();
            
            // Convert milliseconds to seconds, minutes, hours, days, months, years
            const timeDiffSec = Math.floor(timeDiffMs / 1000);
            const timeDiffMin = Math.floor(timeDiffSec / 60);
            const timeDiffHrs = Math.floor(timeDiffMin / 60);  
            const timeDiffDays = Math.floor(timeDiffMs / (1000 * 60 * 60 * 24));
            const timeDiffMonths = Math.abs((currentDate.getMonth() + 1) - (targetDate.getMonth() + 1) + (12 * (currentDate.getFullYear() - targetDate.getFullYear())));
            const timeDiffYears = Math.floor(timeDiffMonths / 12);
            
            if (timeDiffSec < 60){
                return [timeDiffSec, 'seconds']
            }else if (timeDiffMin < 60){
                return [timeDiffMin,'minutes']
            }else if (timeDiffHrs < 24){
                return [timeDiffHrs, 'hours']
            }else if (timeDiffDays < 30){
                return [timeDiffDays, 'days']
            }else if (timeDiffMonths < 12){
                return [timeDiffMonths,'months']
            }else{
                return [timeDiffYears,'years']
            }
}

      }
  
  }
  </script>

  
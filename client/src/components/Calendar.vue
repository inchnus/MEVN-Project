<!-- eslint-disable -->
<template>
  <div class="container">
    

     <div ref="scheduler_here" class="dhx_cal_container" style='width:100%; height:600px;'>
    <div class="dhx_cal_navline">
      <div class="dhx_cal_prev_button">&nbsp;</div>
      <div class="dhx_cal_next_button">&nbsp;</div>
      <div class="dhx_cal_today_button"></div>
      <div class="dhx_cal_date"></div>
      <div class="dhx_cal_tab" name="day_tab" style="right:204px;"></div>
      <div class="dhx_cal_tab" name="week_tab" style="right:140px;"></div>
      <div class="dhx_cal_tab" name="month_tab" style="right:76px;"></div>
      <div class="dhx_cal_tab" name="agenda_tab" style="right:280px;"></div>
    </div>
    <div class="dhx_cal_header"></div>
    <div class="dhx_cal_data"></div>
  </div>

  </div>
</template>

<script>
/* eslint-disable */
import VisitService from '@/services/VisitService'

import 'dhtmlx-scheduler'
//import 'dhtmlx-scheduler/codebase/locale/locale_fr';
//import 'dhtmlx-scheduler/codebase/ext/dhtmlxscheduler_readonly.js';
import 'dhtmlx-scheduler/codebase/ext/dhtmlxscheduler_pdf.js';
import 'dhtmlx-scheduler/codebase/ext/dhtmlxscheduler_agenda_view.js';
import 'dhtmlx-scheduler/codebase/ext/dhtmlxscheduler_editors.js';

export default {
  name: 'Calendar',
  data () {
    return {
      events: []
    }
  },
    mounted() {

    const response = VisitService.getVisits()
    .then(response => {
      this.events = response.data
        scheduler.locale.labels.agenda_tab="Agenda";
         scheduler.config.buttons_left=["dhx_save_btn","dhx_cancel_btn","report_button"];
         scheduler.locale.labels["report_button"] = "Report";
        var status = [
         { key: "Done", label: 'Done' },
         { key: "Not done", label: 'Not done' }
        
        ];
        scheduler.locale.labels.section_priority = 'Status';

       scheduler.config.lightbox.sections = [
      {
        name: "description",
        height: 70,
        map_to: "text",
        type: "textarea",
        focus: true
      },
          { name:"priority", height:58, options:status, 
            map_to:"status", type:"radio", vertical:true},
      {
        name:"time", height:72, type:"time", map_to:"auto"
      }
      ];

      
        scheduler.init(this.$refs.scheduler_here,new Date(),"week");
        
        scheduler.templates.xml_date = function(value){ return new Date(value); };

       
       
        for (var i=0;i<this.events.length;i++)
        {
        this.events[i].id=i;
        
        this.events[i].color="orange";
        if (this.events[i].visitType=="Doctor")
        {
          this.events[i].color="pink";
        }
        if (this.events[i].visitType=="Pharmacist")
        {
          this.events[i].color="green";
          this.events[i].textColor="white";
          
        }
        }
        scheduler.parse(this.events, 'json');
     
        scheduler.attachEvent("onEventAdded", function(id,ev){
        
        });

        scheduler.attachEvent("onEventChanged", function(id,ev){
         const response =  VisitService.putVisits(ev);
         scheduler.updateEvent(id);
       
        });

        scheduler.attachEvent("onConfirmedBeforeEventDelete", function(id,e){
        const response = VisitService.deleteVisits(e)
        scheduler.deleteEvent(id);
         return true;

         scheduler.attachEvent("onLightboxButton", function(button_id, node, e){
           console.log(button_id)
         if(button_id == "report_button"){
        console.log("report")
    }
});
});
   
    })
    .catch(e => {
      this.errors.push(e)     
    })

      
    },
  methods: {

  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
 
 html, body{
            margin:0px;
            padding:0px;
            height:100%;
            overflow:hidden;
           
        }

</style>

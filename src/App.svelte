<script>
    export let appData;
  import Calendar from './DateRangePicker.svelte';
  import MetricsCard from './MetricsCard.svelte';
  import DealLifeCycleDays from './DealLifeCycleDays.svelte';
  import OppurtunityValue from './OppurtunityValue.svelte';
  import StateReasons from './StateReasons.svelte';
  </script>

  <body>
   <Calendar/>
  <MetricsCard {appData}/>
  <DealLifeCycleDays {appData}/>
  <OppurtunityValue {appData}/>
  <StateReasons {appData}/>
  </body>
  <style>
    body{
        background-color: #f6f7f9;
       height: 1700px;
        margin: 0px;
        margin-top:-40px
     }

</style>
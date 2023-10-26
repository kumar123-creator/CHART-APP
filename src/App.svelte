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
  
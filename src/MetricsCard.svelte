<script>
 import Card from './Card.svelte';
  import { onMount,afterUpdate } from 'svelte';
  import { dateStore } from './DateStore.js'; // Import the store
  

 export let appData;
   let data = null; 
   let startDate = "";
   let endDate = "";
   let previousStartDate = '';
  let previousEndDate = '';

   function getDatesFromLocalStorage() {
  const storedStartDate = localStorage.getItem('startDate');
  const storedEndDate = localStorage.getItem('endDate');

  if (storedStartDate && storedEndDate) {
    startDate = storedStartDate;
    endDate = storedEndDate;
    // Fetch data when dates are loaded from local storage

  }
}

    // Subscribe to the dateStore
    dateStore.subscribe((value) => {
  startDate = value.startDate;
  endDate = value.endDate;
    // Check if the dates have changed before making an API call
    if (startDate !== previousStartDate || endDate !== previousEndDate) {
      fetchData(startDate, endDate);
      // Store the dates in local storage for future use
      localStorage.setItem('startDate', startDate);
      localStorage.setItem('endDate', endDate);
    }
});


async function fetchData(startDate, endDate) {

try {
  // Use startDate and endDate in your API calls
  const response = await fetch(`${appData.service.endpoint}/dashboard/sales/metrics?start=${startDate}&end=${endDate}&apiKey=${appData.service.apiKey}`,
    {
      headers: {
        'Cookie': 'SESSION=NTkwN2VlOWQtZjRlNi00NmQ4LWE4MTUtOTJhNT71YjA0ZWMx',
      },
    }
  );
  data = await response.json();
} catch (error) {
  console.error('Error fetching data:', error);
}
}
console.log(data);
    onMount(() => {
      getDatesFromLocalStorage(); // Try to get dates from local storage
      fetchData(startDate, endDate); // Fetch data on component mount
    });
  
    afterUpdate(() => {
   
      localStorage.setItem('startDate', startDate);
      localStorage.setItem('endDate', endDate);
    });
 </script>
 <main>
 {#if data}
 <div class="card-container">
  <div class="card"> 
    <span class="hover-text">Leads Converted</span>
    <i class="fa fa-briefcase"style="margin-left:50px; margin-top:20px;" ></i><h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{data.leadsConverted}</h5>
    <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Leads Converted</p>
    </div>

  <div class="card"> <span class="hover-text">Opportunities</span>
    <i class="fa fa-thumbs-o-up"  style="margin-left:50px; margin-top:20px; " ></i><h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{data.opportunityCountOpen}</h5>
    <p  class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Opportunities</p>
  </div>

  <div class="card"  style="background-color:blue"> <span class="hover-text">Oppurtunity Deal Value</span>
    <h5 style="color:white;margin-top:15px" class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">GBP{data.opportunityValueWon}</h5>
    <p style="color:white;" class="font-normal text-gray-700 dark:text-gray-400 leading-tight">{`Won from ${data.opportunityCountWon} deals`} </p>
  </div>

  <div class="card"><span class="hover-text">Avg. Deal Cycle Days</span>
    <i class="fa fa-bullhorn"style="margin-left:55px; margin-top:20px;" ></i> <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{data.avgDealCycleDays}</h5>
    <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Avg. Deal Cycle</p>
  </div>

  <div class="card"><span class="hover-text">Avg. Deal Value</span>
     <h5 style="color:black;margin-top:15px" class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">GBP{data.avgDealValue}</h5>
    <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Avg. Deal Value</p>
  </div>

  <div class="card"> <span class="hover-text">Avg.Leads Per Opportunity</span>
    <i class="fa fa-user"style="margin-left:60px; margin-top:20px;text-align: center;" ></i>  <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{data.leadsPerOpportunity}</h5>
    <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Leads/Opportunity</p></div>
</div>
{:else}
 <p>Loading data...</p>
{/if}
</main>
<style>
  .card-container {
  display: flex;
  margin: 0 32px; 

}
.card {
  position: relative;
  width: calc(100vw / 6);
  height: 120px;
  background-color:white;
  margin: 10px;
  padding: 20px;
  box-sizing: border-box;
  margin-top: 120px;
 border: 1px solid rgba(20,20,31,.12);
}

.card h5 {
  font-size: 24px; /* Adjust the font size for h5 */

  text-align: center;
  margin-top: -25px;
}
.fa-lg {
    font-size: 1.25em;
    line-height: .05em;
    vertical-align: -0.075em;
}


.card p {
  font-size: 16px; /* Adjust the font size for p */
  color: #888c9b;

  font-weight: 500;
  font-size: 18px;
  font-family: var(--tblr-font-sans-serif) ;
  text-align: center;

}


  .hover-text {
  display: none;
  position: absolute;
  background-color: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 5px;
  border-radius: 5px;
  top: -50%;
  left: 0;
  right: 0;
  text-align: center; /* Center the text horizontally */
  font-size: 16px; /* Adjust the font size for hover text */
  z-index: 1;

}

.card:hover .hover-text {
  display: block;
 
}

  
</style>
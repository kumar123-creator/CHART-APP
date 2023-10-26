<script>
  import { onMount, afterUpdate } from 'svelte';
  import { dateStore } from './DateStore.js'; // Import the store
  import { DateRangePicker } from '@syncfusion/ej2-calendars';
  import { Chart, ColumnSeries, LineSeries, Category, Legend, Tooltip, SplineSeries, BarSeries, DateTime } from '@syncfusion/ej2-charts';
  import { Browser } from '@syncfusion/ej2-base';
  Chart.Inject(ColumnSeries, LineSeries, Category, Legend, Tooltip, SplineSeries, BarSeries, DateTime);
  import Card from './MetricsCard.svelte';
  import { format, parse, compareAsc } from 'date-fns';

  export let appData;
  
  const currentDate = new Date();
  const currentYear = currentDate.getFullYear();

  let startDate = '';
  let endDate = '';
  let previousStartDate = '';
  let previousEndDate = '';

  // Calculate the start and end date objects
  const startDateObj = new Date(startDate);
  const endDateObj = new Date(endDate);

  // Calculate the range of months between the start and end date
  const startYear = startDateObj.getFullYear();
  const endYear = endDateObj.getFullYear();
  const startMonth = startDateObj.getMonth();
  const endMonth = endDateObj.getMonth();

  const monthNames = [];

  for (let year = startYear; year <= endYear; year++) {
    const start = (year === startYear) ? startMonth : 0;
    const end = (year === endYear) ? endMonth : 11;

    for (let month = start; month <= end; month++) {
      monthNames.push(`${year} ${new Date(year, month, 1).toLocaleString('default', { month: 'short' })}`);
    }
  }

  function getDatesFromLocalStorage() {
    const storedStartDate = localStorage.getItem('startDate');
    const storedEndDate = localStorage.getItem('endDate');

    if (storedStartDate && storedEndDate) {
      startDate = storedStartDate;
      endDate = storedEndDate;
    }
  }

  // Subscribe to the dateStore
  dateStore.subscribe((value) => {
    previousStartDate = startDate; // Store the previous start date
    previousEndDate = endDate; // Store the previous end date
    startDate = value.startDate;
    endDate = value.endDate;

    // Check if the dates have changed before making an API call
    if (startDate !== previousStartDate || endDate !== previousEndDate) {
      fetchOpportunityChartData(startDate, endDate);
      // Store the dates in local storage for future use
      localStorage.setItem('startDate', startDate);
      localStorage.setItem('endDate', endDate);
    }
  });

  onMount(() => {
    getDatesFromLocalStorage(); // Try to get dates from local storage
  });

  afterUpdate(() => {
      fetchOpportunityChartData(startDate, endDate);
      // Store the dates in local storage for future use
      localStorage.setItem('startDate', startDate);
      localStorage.setItem('endDate', endDate);
    
  });

  async function fetchOpportunityChartData(startDate, endDate) {
    // Use selectedStartDate and selectedEndDate in the API call
    const apiUrlDays = `${appData.service.endpoint}/dashboard/sales/data/opportunitymonthlymetrics?start=${startDate}&end=${endDate}&apiKey=${appData.service.apiKey}`;
    const responseDays = await fetch(apiUrlDays);
    const dataDays = await responseDays.json();

    const data = dataDays.map(item => {
      const month = parseInt(item.monthLabel.split('/')[0]) - 1;
      const year = parseInt(item.monthLabel.split('/')[1]);
      const monthLabel = new Date(year, month, 1);
      const formattedDate = new Intl.DateTimeFormat('en-US', {
        year: 'numeric',
        month: 'short'
      }).format(monthLabel);

      return {
        x: formattedDate, // Format as "Jan 2023" (month name and year)
        opportunities: item.opportunities,
        days: item.days
      };
    });

    // Sort the data by the 'x' property (which represents formatted month and year)
    data.sort((a, b) => {
      const dateA = new Date('01 ' + a.x);
      const dateB = new Date('01 ' + b.x);
      return dateA - dateB;
    });

    console.log(data);

    const chartDays = new Chart({
      primaryXAxis: {
        valueType: 'Category',
        majorGridLines: { width: 0 }
      },
      primaryYAxis: {
        labelFormat: '{value}',
        title: 'Opportunities',
        titleStyle: {
                     fontFamily: "'Segoe UI', 'Helvetica Neue', 'Trebuchet MS', Verdana, sans-serif",
                   fontWeight: '360',
                   color: "#767676;",
                   size: '18px',
                  
                  },
                 labelStyle: {
                     size: '15px',
                  fontWeight:"normal"  
             },
        edgeLabelPlacement: 'Shift',
        majorTickLines: { width: 0 },
        lineStyle: { width: 0 },
        majorGridLines: { width: 1 },
      },
      series: [
        {
          type: 'Column',
          dataSource: data,
          xName: 'x',
          yName: 'opportunities',
          name: 'Opportunities',
          fill: 'DodgerBlue',
          columnSpacing: 0.1,
        },
        {
          type: 'Spline',
          dataSource: data,
          xName: 'x',
          yName: 'days',
          name: 'Avg Days to Deal',
          yAxisName: 'rightYAxis',
          fill: 'Tomato',
          marker: {
            visible: true,
            height: 10,
            width: 10,
          },
        }
      ],
      axes: [
        {
          name: 'rightYAxis',
          opposedPosition: true,
          title: 'Total Time to Deal Days',
          titleStyle: {
                     fontFamily: "'Segoe UI', 'Helvetica Neue', 'Trebuchet MS', Verdana, sans-serif",
                   fontWeight: '360',
                   color: "#767676;",
                   size: '18px',
                  
                  },
                     labelStyle: {
                     size: '15px',
                  fontWeight:"normal"
          
                     },
          majorGridLines: { width: 1 },
      
        },
      ],
      legendSettings: {
        visible: true,
      },
      tooltip: {
        enable: true,
        shared: true,
        format: '${series.name}: ${point.y}'
      },
      width: '100%',
      height: '300px'
    });

    chartDays.appendTo('#chart-container-days');
  }
</script>
 
<div class="card card-fluid">
  <div class="card-header border-0">
    Deal Lifecycle Days
  </div>
  <div class="card-body">
      <div id="chart-container-days"></div>
  </div>
</div>


<style>
     

.card {
  position: relative;
  display: flex;
  flex-direction: column;
  min-width: 0;

  background-color: #fff;
  background-clip: border-box;
  border: 1px solid rgba(20,20,31,.12);
  border-radius: 0.25rem;
  margin: 40px;
 margin-top:10px;
  

}
.card-body {
  flex: 1 1 auto;
  min-height: 1px;
  padding: 1rem;
}
.card-header {
font-size: 16px; /* Adjust the font size as needed */
font-weight: bold; /* Adjust the font weight as needed */


padding: 15px; /* Adjust the padding as needed */

}

</style>
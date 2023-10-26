<script>
  import { onMount, afterUpdate } from 'svelte';
  import { DateRangePicker } from '@syncfusion/ej2-calendars';
  import { dateStore } from './DateStore.js'; // Import the store

  let daterangepicker;
  const currentDate = new Date();
  const currentYear = currentDate.getFullYear();
  const currentMonth = currentDate.getMonth() + 1; // Adding 1 because months are zero-indexed

  const lastYearStartMonth = `${currentYear - 1}-${currentMonth.toString().padStart(2, '0')}`;
  const currentMonthDate = `${currentYear}-${currentMonth.toString().padStart(2, '0')}`;

  // Initialize startDate and endDate with values from local storage
  let startDate = localStorage.getItem('startDate') || lastYearStartMonth;
  let endDate = localStorage.getItem('endDate') || currentMonthDate;

  let formattedStartDate = formatStoredDate(startDate);
  let formattedEndDate = formatStoredDate(endDate);

  onMount(() => {
    initializeDateRangePicker(startDate, endDate);
  });

  afterUpdate(() => {
    const updatedStartDate = localStorage.getItem('startDate') || '';
    const updatedEndDate = localStorage.getItem('endDate') || '';

    if (startDate !== updatedStartDate || endDate !== updatedEndDate) {
      startDate = updatedStartDate;
      endDate = updatedEndDate;
      formattedStartDate = formatStoredDate(updatedStartDate);
      formattedEndDate = formatStoredDate(updatedEndDate);
    }
  });

  function initializeDateRangePicker(initialStartDate, initialEndDate) {
    daterangepicker = new DateRangePicker({
      placeholder: 'Select Range',
      start: 'Year',
      depth: 'Year',
      format: 'MMM yyyy',
      value: [
        new Date(initialStartDate),
        new Date(initialEndDate)
      ],
      change: () => {
        const selectedDates = daterangepicker.getSelectedRange();
        if (selectedDates && selectedDates.startDate && selectedDates.endDate) {
          const selectedStartDate = formatSelectedDate(selectedDates.startDate);
          const selectedEndDate = formatSelectedDate(selectedDates.endDate);
          localStorage.setItem('startDate', selectedStartDate);
          localStorage.setItem('endDate', selectedEndDate);
          dateStore.set({ startDate: selectedStartDate, endDate: selectedEndDate });
        }
      }
    });
    daterangepicker.appendTo('#element');
  }

  function formatSelectedDate(date) {
    const day = date.getDate();
    const month = date.getMonth() + 1;
    const year = date.getFullYear();
    return `${day.toString().padStart(2, '0')}/${month.toString().padStart(2, '0')}/${year}`;
  }

  function formatStoredDate(date) {
    const [day, month, year] = date.split('/');
    const monthName = new Date(`${year}-${month}-01`).toLocaleDateString('default', { month: 'short' });
    return `${monthName} ${year}`;
  }
</script>

<div class="calendar-container">
  <div class="col-lg-12 control-section">
    <div id="wrapper">
      <input id="element" type="text" value="{formattedStartDate} - {formattedEndDate}"/>
    </div>
  </div>
</div>

<style>

  .calendar-container {
      position: absolute;
      top: 50px; 
      right: 15px; 
      width: 300px;
      height: 60px;
      text-align: center;
      align-items: center; 
    
  
    }
    
  #element{
      width :210px;
      height: 40px;
      border-color: #ced4da ;
      background: linear-gradient(180deg,#fff,#f6f7f9) ;
      transition: box-shadow .2s ease-in-out,border-color .2s ease-in-out ;
      box-shadow: inset 0 1px 0 0 rgba(34,34,48,.05);
      border: 1px solid;
      border-width: 1px;
      margin: 10PX;
      position: relative; 
      font-size: 16px;
      font-weight: 400px;
      font-family: Arial, sans-serif;
      color: #333;
      top:0px;
      border-radius: 0.25rem;
   
    }
</style>
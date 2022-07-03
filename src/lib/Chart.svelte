<script>
  let canvas

  import Chart from 'chart.js/auto/auto.js'
  import { onMount } from 'svelte'

  //         {
  // txn_date:string;
  // new_case:string;
  // total_case:string;
  // new_case_excludeabroad:string;
  // total_case_excludeabroad:string;
  // new_death:string;
  // total_death:string;
  // new_recovered:string;
  // total_recovered:string;
  // update_date:string}[]

  const getXAxes = (res) => {
    // return mapValues(groupBy(res, 'txn_date'), res.map(dailyData => omit(dailyData, 'txn_date')));
    return res.map((data) => data.txn_date)
  }

  const getYAxes = (res) => {
    // return mapValues(groupBy(res, 'new_case'), res.map(dailyData => omit(dailyData, 'txn_date')));
    return res.map((data) => data.new_case)
  }

  onMount(async () => {
    
    const res = await fetch(
      `https://covid19.ddc.moph.go.th/api/Cases/timeline-cases-all`
    )
    const json = await res.json()

    var ctx = canvas.getContext('2d')

    const config = {
      type: 'line',
      data: {
        labels: getXAxes(json),
        datasets: [
          {
            label: 'new case',
            data: getYAxes(json),
            backgroundColor: ['rgba(255, 99, 132, 0.2)'],
            borderColor: ['rgba(255, 150, 0, 1)'],
            borderWidth: 1,
          },
          {
            label: 'new death',
            data: json.map((data) => data.new_death),
            backgroundColor: ['rgba(255, 120, 0, 1)'],
            borderColor: ['rgba(255, 0, 0, 1)'],
            borderWidth: 1,
          },
          {
            label: 'new recovered',
            data: json.map((data) => data.new_recovered),
            backgroundColor: ['rgba(0, 255, 130, 0.8)'],
            borderColor: ['rgba(0, 100, 0, 1)'],
            borderWidth: 1,
          },
        ],
      },
      options: {
        borderRadius: '30',
        responsive: true,
        cutout: '95%',
        spacing: 2,
        plugins: {
          title: {
            display: true,
            text: 'Daily Covid 19',
          },
        },
      },
    }

    var myChart = new Chart(ctx, config)
    return
  })
</script>

<canvas bind:this={canvas} width="300" height="120" />

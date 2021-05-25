<script>
  import { DateTime, Duration, Info } from 'luxon'

  function getMonthGrid(date) {
    const grid = []
    const first = date.startOf('month')
    const end = first.endOf('month').endOf('week')
    let curr = first.startOf('week')
    const count = Math.round(end.diff(curr).as('days'))
    let index = 0
    let week = 0
    let weekday
    while (index < count) {
      if (curr.weekday === 1) {
        grid.push(new Array(7))
        week = grid.length - 1
        weekday = 0
      }

      grid[week][weekday] = {
        date: curr
      }

      curr = curr.plus(Duration.fromObject({ days: 1 }))
      weekday++
      index++
    }

    return grid
  }

  function update() {
    weeks = getMonthGrid(date)

    const lastMonth = date.minus({ months: 1 })
    lastMonthFormatted = lastMonth.toFormat('MMMM yyyy')

    const nextMonth = date.plus({ months: 1 })
    nextMonthFormatted = nextMonth.toFormat('MMMM yyyy')

    monthFormatted = date.toFormat('MMMM yyyy')

    const lastYear = date.minus({ years: 1 })
    lastYearFormatted = lastYear.toFormat('yyyy')

    const nextYear = date.plus({ years: 1 })
    nextYearFormatted = nextYear.toFormat('yyyy')

    yearFormatted = date.toFormat('yyyy')

    const year = date.get('year')
    const startOfDecade = year - (year % 10)
    console.log(startOfDecade)
    for (let i = 0; i < 10; i++) {
      decade[i] = startOfDecade + i
    }
    lastDecade = startOfDecade - 10
    nextDecade = startOfDecade + 10
  }

  function handleLastMonthClick() {
    date = date.minus({ months: 1 })
    update()
  }

  function handleNextMonthClick() {
    date = date.plus({ months: 1 })
    update()
  }

  function handleMonthClick(index) {
    console.log(index)
    date = date.set({ month: index + 1 })
    update()

    showSelector = false
    showYearSelector = false
  }

  function handleLastYearClick() {
    date = date.minus({ years: 1 })
    update()
  }

  function handleNextYearClick() {
    date = date.plus({ years: 1 })
    update()
  }

  function handleYearClick(year) {
    date = date.set({ year })
    update()

    showYearSelector = false
  }

  function handleLastDecadeClick() {
    date = date.minus({ year: 10 })
    update()
  }

  function handleNextDecadeClick() {
    date = date.plus({ year: 10 })
    update()
  }

  function toggleSelector() {
    showSelector = !showSelector
  }

  function toggleYearSelector() {
    showYearSelector = !showYearSelector
  }

  function isToday(otherDate) {
    return DateTime.local().startOf('day').equals(otherDate.startOf('day'))
  }

  export const weekdaysFormatted = Info.weekdays('short')
  export const monthsFormatted = Info.months('short')
  export let date = DateTime.local()

  export let weeks = []
  export let lastMonthFormatted = ''
  export let nextMonthFormatted = ''
  export let monthFormatted = ''
  export let lastYearFormatted = ''
  export let nextYearFormatted = ''
  export let yearFormatted = ''
  export let decade = new Array(10)
  export let lastDecade = 0
  export let nextDecade = 0

  export let showSelector = false
  export let showYearSelector = false

  update()
</script>

<main>
  <div class="header" class:hidden={showSelector}>
    <div>
      <h1 class="month-title">{monthFormatted}</h1>
    </div>
    <div class="controls">
      <button class="change-month" on:click={handleLastMonthClick}
        >&laquo; {lastMonthFormatted}</button
      >
      <button class="change-month" on:click={toggleSelector}
        >Select Month</button
      >
      <button class="change-month" on:click={handleNextMonthClick}
        >{nextMonthFormatted} &raquo;</button
      >
    </div>
  </div>
  <div class="selector" class:hidden={!showSelector}>
    <div class="header">
      <div>
        <h1 class="year-title">{yearFormatted}</h1>
      </div>
      <div class="controls">
        <button class="change-year" on:click={handleLastYearClick}
          >&laquo; {lastYearFormatted}</button
        >
        <button class="change-year" on:click={toggleYearSelector}
          >Select Year</button
        >
        <button class="change-year" on:click={handleNextYearClick}
          >{nextYearFormatted} &raquo;</button
        >
      </div>
    </div>
    <div class="month-selector" class:hidden={showYearSelector}>
      {#each monthsFormatted as month, index}
        <button on:click={() => handleMonthClick(index)}>{month}</button>
      {/each}
    </div>
    <div class="year-selector" class:hidden={!showYearSelector}>
      <button on:click={handleLastDecadeClick}>&laquo; {lastDecade}</button>
      {#each decade as year}
        <button on:click={() => handleYearClick(year)}>{year}</button>
      {/each}
      <button on:click={handleNextDecadeClick}>{nextDecade} &raquo;</button>
    </div>
  </div>
  <div class="calendar">
    {#each weekdaysFormatted as weekday}
      <div class="calendar-heading">{weekday}</div>
    {/each}
    {#each weeks as week}
      {#each week as day}
        <div class="calendar-day" class:calendar-today={isToday(day.date)}>
          {day.date.toFormat('d')}
        </div>
      {/each}
    {/each}
  </div>
</main>

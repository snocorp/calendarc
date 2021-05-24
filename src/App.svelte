<script>
  import { DateTime, Duration, Info } from 'luxon'

  function getLang() {
    if (navigator.languages != undefined) return navigator.languages[0]
    else return navigator.language
  }

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
  }

  function handleLastMonthClick() {
    date = date.minus({ months: 1 })
    update()
  }

  function handleNextMonthClick() {
    date = date.plus({ months: 1 })
    update()
  }

  function isToday(otherDate) {
    return DateTime.local().startOf('day').equals(otherDate.startOf('day'))
  }

  export const weekdaysFormatted = Info.weekdays('short')
  export let date = DateTime.local()

  export let weeks = []
  export let lastMonthFormatted = ''
  export let nextMonthFormatted = ''
  export let monthFormatted = ''

  update()
</script>

<main>
  <div class="header">
    <div>
      <h1 class="month-title">{monthFormatted}</h1>
    </div>
    <div class="controls">
      <button class="change-month" on:click={handleLastMonthClick}
        >&laquo; {lastMonthFormatted}</button
      >
      <button class="change-month" on:click={handleNextMonthClick}
        >{nextMonthFormatted} &raquo;</button
      >
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

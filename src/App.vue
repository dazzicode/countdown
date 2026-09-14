
<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

/*
|--------------------------------------------------------------------------
| CONFIGURAÇÃO
|--------------------------------------------------------------------------
*/

// Início da contagem
const startDate = new Date('2026-01-17T00:00:00-03:00')

// Data da viagem
const targetDate = new Date('2026-12-30T00:00:00-03:00')

const now = new Date()
const total = datetimeToDays(targetDate, startDate)
const todayIsDayNum = datetimeToDays(targetDate, now)


/*
|--------------------------------------------------------------------------
| ESTADO
|--------------------------------------------------------------------------
*/

const timeRemaining = ref({
  days: 0,
  hours: 0,
  minutes: 0,
  seconds: 0
})

const progress = ref(0)

let interval = null


/*
|--------------------------------------------------------------------------
| COUNTDOWN
|--------------------------------------------------------------------------
*/
function datetimeToDays(d1, d2) {
  let diffByMillis = d2 - d1;
  let millisecondsByDay = 24 * 60 * 60 * 1000;
  return diffByMillis / millisecondsByDay;
}



function updateCountdown() {
  const now = new Date()

  const ytotal = datetimeToDays(targetDate, startDate)
  const dayNo = datetimeToDays(targetDate, now)

  const progressBar = ( 1 - (dayNo/ytotal)) * 100.0

  const difference =
      targetDate.getTime() - now.getTime()

  /*
   * Duração total do período
   */
  const totalDuration =
      targetDate.getTime() -
      startDate.getTime()

  /*
   * Tempo já passado
   */
  const elapsed =
      now.getTime() -
      startDate.getTime()

  /*
   * Progresso de 0 a 100
   */
  progress.value = progressBar
  console.log("progress", progress.value)


  /*
   * Quando chegar na data
   */
  if (difference <= 0) {

    timeRemaining.value = {
      days: 0,
      hours: 0,
      minutes: 0,
      seconds: 0
    }

    progress.value = 100

    return
  }


  /*
   * Converte tudo para segundos
   */
  const totalSeconds =
      Math.floor(difference / 1000)


  /*
   * Calcula cada unidade
   */
  timeRemaining.value = {

    days: Math.floor(
        totalSeconds / 86400
    ),

    hours: Math.floor(
        (totalSeconds % 86400) / 3600
    ),

    minutes: Math.floor(
        (totalSeconds % 3600) / 60
    ),

    seconds:
        totalSeconds % 60
  }
}


/*
|--------------------------------------------------------------------------
| FORMATAÇÃO
|--------------------------------------------------------------------------
*/

const formattedHours = computed(() =>
    String(
        timeRemaining.value.hours
    ).padStart(2, '0')
)

const formattedMinutes = computed(() =>
    String(
        timeRemaining.value.minutes
    ).padStart(2, '0')
)

const formattedSeconds = computed(() =>
    String(
        timeRemaining.value.seconds
    ).padStart(2, '0')
)


/*
|--------------------------------------------------------------------------
| CICLO DE VIDA
|--------------------------------------------------------------------------
*/

onMounted(() => {

  updateCountdown()

  interval = setInterval(
      updateCountdown,
      1000
  )

})

onUnmounted(() => {

  clearInterval(interval)

})
</script>


<template>

  <main class="page">

    <!-- =====================================================
         COUNTDOWN
    ====================================================== -->

    <section class="countdown">


      <!-- DIAS -->

      <div class="days-container">

        <Transition
            name="number"
            mode="out-in"
        >

          <div
              :key="timeRemaining.days"
              class="days"
          >
            {{ timeRemaining.days }}
          </div>

        </Transition>


        <div class="days-label">
          DIAS
        </div>

      </div>


      <!-- HORAS / MINUTOS / SEGUNDOS -->

      <div class="time">

        <span>
          {{ formattedHours }}
        </span>


        <span class="separator">
          :
        </span>


        <span>
          {{ formattedMinutes }}
        </span>


        <span class="separator">
          :
        </span>


        <Transition
            name="number"
            mode="out-in"
        >

          <span
              :key="formattedSeconds"
          >
            {{ formattedSeconds }}
          </span>

        </Transition>

      </div>


      <!-- DATA -->

      <div class="date">

        30 DE DEZEMBRO

        <span>
          2026
        </span>

      </div>

    </section>


    <!-- =====================================================
         BARRA DE PROGRESSO / ROTA DO AVIÃO
    ====================================================== -->

    <footer class="flight-progress">

      <div class="progress-container">


        <!-- Linha da rota -->



          <div
              class="progress-line"
              :style="{
              width: `100%`
            }"
          />




        <!-- Avião -->

        <div
            class="plane"
            :style="{
            left: `${progress}%`
          }"
        >
          ✈︎
        </div>

      </div>

    </footer>

  </main>

</template>


<style>

/*
|--------------------------------------------------------------------------
| RESET
|--------------------------------------------------------------------------
*/

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}


html,
body,
#app {

  width: 100%;
  height: 100%;
  background: #000;
}


body {

  margin: 0;

  background: #000;

}


/*
|--------------------------------------------------------------------------
| PÁGINA
|--------------------------------------------------------------------------
*/

.page {

  width: 100%;

  height: 100vh;

  height: 100dvh;

  display: flex;

  align-items: center;

  justify-content: center;



  color: #ffffff;

  font-family:
      -apple-system,
      BlinkMacSystemFont,
      "SF Pro Display",
      "SF Pro Text",
      "Helvetica Neue",
      Arial,
      sans-serif;

  overflow: hidden;

}


/*
|--------------------------------------------------------------------------
| COUNTDOWN
|--------------------------------------------------------------------------
*/

.countdown {

  display: flex;

  flex-direction: column;

  align-items: center;

  text-align: center;

  transform: translateY(-5%);

}


/*
|--------------------------------------------------------------------------
| DIAS
|--------------------------------------------------------------------------
*/

.days-container {

  display: flex;

  flex-direction: column;

  align-items: center;

}


.days {

  font-size:
      clamp(
          120px,
          18vw,
          220px
      );

  font-weight: 600;

  line-height: 0.85;

  letter-spacing: -0.075em;

  font-variant-numeric:
      tabular-nums;

  color: #ffffff;

}


.days-label {

  margin-top: 26px;

  font-size: 13px;

  font-weight: 500;

  letter-spacing: 0.32em;

  padding-left: 0.32em;

  color: #ffffff;

}


/*
|--------------------------------------------------------------------------
| HORÁRIO
|--------------------------------------------------------------------------
*/

.time {

  display: flex;

  align-items: center;

  margin-top: 48px;

  font-size:
      clamp(
          28px,
          4vw,
          44px
      );

  font-weight: 400;

  letter-spacing: -0.025em;

  font-variant-numeric:
      tabular-nums;

  color: #ffffff;

}


.time .separator {

  margin: 0 9px;

  color: #50C878;

  font-weight: 300;

}


/*
|--------------------------------------------------------------------------
| DATA
|--------------------------------------------------------------------------
*/

.date {

  margin-top: 48px;

  font-size: 11px;

  font-weight: 500;

  letter-spacing: 0.22em;

  padding-left: 0.22em;

  color: #ffffff;

}


.date span {

  display: block;

  margin-top: 7px;

  font-size: 10px;

  color: #ffffff;

}


/*
|--------------------------------------------------------------------------
| BARRA DE VOO
|--------------------------------------------------------------------------
*/

.flight-progress {

  position: fixed;

  left: 0;

  right: 0;

  bottom: 34px;

  padding: 0 42px;

}


.progress-container {

  position: relative;

  width: 100%;

  height: 30px;

}


/*
|--------------------------------------------------------------------------
| LINHA
|--------------------------------------------------------------------------
*/

.progress-line {

  position: absolute;

  top: 50%;

  left: 0;

  right: 0;

  height: 1px;

  background: #50C878;
  box-shadow: 0 0 .2rem #fff,
  0 0 .2rem #fff,
  0 0 2rem #50C878,
  0 0 0.8rem #50C878,
  0 0 2.8rem #50C878,
  inset 0 0 1.3rem #50C878;

  transform:
      translateY(-50%);

}


.progress-line-active {

  height: 100%;

  background: #1d1d1f;

  transition:
      width 1s linear;

}


/*
|--------------------------------------------------------------------------
| AVIÃO
|--------------------------------------------------------------------------
*/

.plane {

  position: absolute;

  top: 50%;

  transform:
      translate(
          -50%,
          -50%
      );

  font-size: 30px;

  line-height: 1;

  color: #ffffff;

  transition:
      left 1s linear;

  /*background: #ffffff;*/

  /*padding: 0 8px;*/
  text-shadow:
      0 0 46px #50C878,
      0 0 80px #50C878,
      0 0 21px #50C878,
      0 0 42px #0fa,
      0 0 82px #0fa,
      0 0 92px #0fa,
      0 0 102px #0fa,
      0 0 151px #0fa;
}


/*
|--------------------------------------------------------------------------
| ANIMAÇÃO DOS NÚMEROS
|--------------------------------------------------------------------------
*/

.number-enter-active,
.number-leave-active {

  transition:
      opacity 0.18s ease,
      transform 0.18s ease;

}


.number-enter-from {

  opacity: 0;

  transform:
      translateY(8px);

}


.number-leave-to {

  opacity: 0;

  transform:
      translateY(-8px);

}


/*
|--------------------------------------------------------------------------
| MOBILE
|--------------------------------------------------------------------------
*/

@media (max-width: 600px) {

  .countdown {

    transform:
        translateY(-5%);

  }


  .days-label {

    margin-top: 20px;

    font-size: 11px;

  }


  .time {

    margin-top: 38px;

    font-size: 30px;

  }


  .time .separator {

    margin: 0 6px;

  }


  .date {

    margin-top: 40px;

    font-size: 10px;

  }


  .flight-progress {

    bottom: 25px;

    padding: 0 20px;

  }


  .plane {

    font-size: 17px;

  }

}

</style>


<template>
    <div class="countdown">
        <div v-if="past">
            <h1>{{ message }}</h1>
        </div>
        <div v-else class="interval" v-for="time in timeValues">
            <span class="number">{{ time.time }}</span>
            <div class="format">{{ time.display }}</div>
        </div>
    </div>
</template>

<script>
    export default {
        props: {
          eventTime: {
              type: String,
              required: true
          }
        },
        data () {
            return {
                timeValues : [],
                start: "",
                interval: "",
                offset: "",
                message: "",
                past: false,
            }
        },
        mounted() {
            let start = new Date(this.eventTime);
            this.offset = start.getTimezoneOffset() * 60000;
            this.start = start.getTime() + this.offset;
            this.timerCount()
            this.interval = setInterval(() => {
                this.timerCount();
            }, 1000);
        },
        methods: {
            timerCount: function () {
                let now = new Date().getTime();
                let diff = this.start - now;

                if (diff >= 0) {
                    this.populateTime(this.start - now);
                } else if (diff < 1000 * 60 * 60 * 24 * 7 * -1) {
                    this.past = true;
                    this.message = "That was fun.";
                    clearInterval(this.interval);
                } else {
                    this.past = true;
                    this.message = "It's time!!";
                    clearInterval(this.interval);
                }
            },
            populateTime: function(timeRemaining){
                this.reset()
                // Time calculations for days, hours, minutes and seconds
                this.setTimeValues("weeks", Math.floor(timeRemaining / (1000 * 60 * 60 * 24 * 7)));
                this.setTimeValues("days", Math.floor(timeRemaining % (1000 * 60 * 60 * 24 * 7) / (1000 * 60 * 60 * 24)));
                this.setTimeValues("hours", Math.floor(timeRemaining % (1000 * 60 * 60 * 24) / (1000 * 60 * 60)));
                this.setTimeValues("minutes", Math.floor(timeRemaining % (1000 * 60 * 60) / (1000 * 60)));
                this.setTimeValues("seconds", Math.floor(timeRemaining % (1000 * 60) / 1000));
            },
            setTimeValues: function (display, time) {
                this.timeValues.push(
                    {
                        time: this.displayZeros(time),
                        display: display
                    }
                )
            },
            reset: function () {
                this.timeValues = [];
            },
            displayZeros: function (value) {
                return ("0" + value).slice(-2);
            }
        },
    }
</script>
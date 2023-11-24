<template>
  <div class="container">
    


    <div class="row">
      <div class="col-sm-6">
    <div class="wrapper">
        <img src="./../assets/earth.png" alt="clock">
        <h1>{{ hours }}:{{ minutes }}:{{ seconds }} </h1>
        <div class="content">
          <div class="column">
            <select>
              <option value="Hour" selected disabled hidden>Hour</option>
            </select>
          </div>
          <div class="column">
            <select>
              <option value="Minute" selected disabled hidden>Minute</option>
            </select>
          </div>
        
            <div class="column shadow-none p-1 mb-2 bg-light rounded"> 
              {{ day }}/{{ month }}/{{ year }}
            </div>
        
        </div>
        <button @click="setAlarm"> {{ buttonAlarm }}</button>
       
      </div>
    </div>
    <div class="col-sm-6">
    <div class="wrapper">
        <img src="./../assets/alien.png" alt="clock">
        <!-- <h1>00:00:00</h1> -->
        <h1>{{ hours1 }}:{{ minutes1 }}:{{ seconds1 }}</h1>
        <div class="content">
          <div class="column">
            <select>
              <option value="Hour" selected disabled hidden>Hour</option>
            </select>
          </div>
          <div class="column">
            <select>
              <option value="Minute" selected disabled hidden>Minute</option>
            </select>
          </div>
          <div class="column shadow-none p-1 mb-2 bg-light rounded"> 
              {{ alienDates.alienDay }}/{{ Math.trunc(alienDates.alienMonth, 2) }}/{{ alienDates.alienYear }}
            </div>
        </div>
        <button @click="setAlarmAlien"> {{ buttonAlarmAlien }}</button>
      </div>
      </div>
    </div>
</div>
   
</template>

<script>
import sound from './../assets/ringtone.mp3';
var ringtone = new Audio(sound);

const zeroPadding = (num, digit) => {
  return String(num).padStart(digit, "0");
};

const earthToAlienMonthAndDay = (earthMonth, earthDay) =>{
  // Define the number of days in each Alien month
  const daysInAlienMonths = [44, 42, 48, 40, 48, 44, 40, 44, 42, 40, 40, 42, 44, 48, 42, 40, 44, 38];

  // Assuming 18 months in an Alien year
  var alienYear = Math.floor((earthMonth - 1) / (12 / 18)) + 1;
  //var alienYear = Math.floor(earthMonth * (18 / 12)) + 1;

  // Calculate the total days in preceding Alien months
  var totalDaysInPrecedingMonths = daysInAlienMonths.slice(0, (earthMonth - 1) % (12 / 18)).reduce((acc, days) => acc + days, 0);

  // Calculate the Alien month and day
  var alienMonth = (earthMonth - 1) % (12 / 18) + 1;
  var alienDay = totalDaysInPrecedingMonths + earthDay;

  return {
    alienYear: alienYear,
    alienMonth: alienMonth,
    alienDay: alienDay
  };
};

export default {
  props: ["location", "diff"],
  data() {
    return {
      date: new Date(),
      alienDate: '',
      buttonAlarm : 'Set Alarm',
      buttonAlarmAlien: 'Set Alarm',
      isAlarmTime : "",
      isAlarmSet : false,
      isAlarmAlienSet : false,
      
    };
  },
  
  computed: {
    year() {
      return this.date.getFullYear();
    },
    month() {
      return zeroPadding(this.date.getMonth() + 1, 2);
    },
    day() {
      return zeroPadding(this.date.getDate(), 2);
    },
    hours() {
      return zeroPadding(this.date.getHours(), 2);
    },
    minutes() {
      return zeroPadding(this.date.getMinutes(), 2);
    },
    seconds() {
      return zeroPadding(this.date.getSeconds(), 2);
    },

    alienDates() {
      return earthToAlienMonthAndDay(this.date.getMonth(), this.date.getDay());
    },
   
    hours1() {
      return zeroPadding(this.earthToAlienHours(this.date.getHours()), 2);
    },
    minutes1() {
      return zeroPadding(this.earthToAlienMinutes(this.date.getMinutes()), 2);
    },
    seconds1() {
      return zeroPadding(this.earthToAlienSecond(this.date.getSeconds()), 2);
    },

  },
  mounted() {
    this.setDate();
    setInterval(() => this.setDate(), 1000);

    this.setAlienTime();
    setInterval(() => this.setAlienTime(), 1000);
  },

 
  methods: {
    setDate() {
      this.date = new Date();
      this.date.setHours(this.date.getHours() + this.diff)
    },
    setAlienDate() {
      this.date = new Date();
      var alienDate = earthToAlienMonthAndDay(this.date.getMonth(), this.date.getDay());
      
      return alienDate;
      },

      earthToAlienMonth(earthMonth){
      return earthMonth * (18 / 12);
    },


    setAlienTime() {
      this.alienDate = new Date();
       var earthHours = this.alienDate.getHours();    
       this.alienDate.setHours(this.earthToAlienHours(earthHours) + this.diff)
    },
   
    earthToAlienSecond(earthSeconds) {
      return Math.trunc(earthSeconds * (90 / 60));
    },
    earthToAlienMinutes(earthMinutes) {
      return Math.trunc(earthMinutes * (90 / 60));
    },
   earthToAlienHours(earthHours){
      return Math.trunc(earthHours * (36 / 24));
    },

    validateAndSetDateTime() {
      var dateInput = document.getElementById("dateInput").value;
      var timeInput = document.getElementById("timeInput").value;
      var errorMessage = document.getElementById("error-message");

      // Validate date and time
      if (dateInput === "" || timeInput === "") {
        errorMessage.textContent = "Please enter both date and time.";
      } else {
        var selectedDateTime = new Date(dateInput + " " + timeInput);
        var currentDateTime = new Date();

        if (selectedDateTime < currentDateTime) {
          errorMessage.textContent = "Please select a future date and time.";
        } else {
          errorMessage.textContent = ""; // Clear any previous error message

          // Use the selectedDateTime for further processing (e.g., setting an alarm)
          console.log("Selected Date and Time:", selectedDateTime);
        }
      }
    },

    setAlarm() {
      // ringtone.play();
      if (this.isAlarmSet) {
        this.alarmTime = "";
        ringtone.pause();
        // content.classList.remove("disable");
        // setAlarmBtn.innerText = "Set Alarm";
        this.buttonAlarm = "Set Alarm"
        return this.isAlarmSet = false;
    }
  

    // let time = `${selectMenu[0].value}:${selectMenu[1].value} ${selectMenu[2].value}`;
    // if (time.includes("Hour") || time.includes("Minute") || time.includes("AM/PM")) {
    //     return alert("Please, select a valid time to set Alarm!");
    // }
   // alarmTime = time;
    this.isAlarmSet = true;
   // content.classList.add("disable");

      this.buttonAlarm = "Clear Alarm"

    // if (isAlarmSet) {
    //     alarmTime = "";
    //     ringtone.pause();
    //     content.classList.remove("disable");
    //     setAlarmBtn.innerText = "Set Alarm";
    //     return isAlarmSet = false;
    // }

    // let time = `${selectMenu[0].value}:${selectMenu[1].value} ${selectMenu[2].value}`;
    // if (time.includes("Hour") || time.includes("Minute") || time.includes("AM/PM")) {
    //     return alert("Please, select a valid time to set Alarm!");
    // }
    // alarmTime = time;
    // isAlarmSet = true;
    // content.classList.add("disable");
    // setAlarmBtn.innerText = "Clear Alarm";
},
setAlarmAlien() {
 
  if (this.isAlarmAlienSet) {
        this.alarmTime = "";
        ringtone.pause();
        this.buttonAlarmAlien = "Set Alarm"
        return this.isAlarmSet = false;
    }
      this.isAlarmAlienSet = true;
      this.buttonAlarmAlien = "Clear Alarm"
   }




  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

/* -------------------------- */
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600&display=swap');
/* *{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Poppins', sans-serif;
} */
body, .wrapper, .content{
  display: flex;
  align-items: center;
  justify-content: center;
}
body{
  /* padding: 0 10px; */
  min-height: 100vh;
  background: #4A98F7;
}
.wrapper{
  width: 440px;
  padding: 30px 30px 38px;
  background: #fff;
  border-radius: 10px;
  flex-direction: column;
  box-shadow: 0 10px 25px rgba(0,0,0,0.1);
}
.wrapper img{
  max-width: 103px;
}
.wrapper h1{
  font-size: 38px;
  font-weight: 500;
  margin: 30px 0;
}
.wrapper .content{
  width: 100%;
  justify-content: space-between;
}
.content.disable{
  cursor: no-drop;
}
.content .column{
  padding: 0 10px;
  border-radius: 5px;
  border: 1px solid #bfbfbf;
  width: calc(100% / 3 - 5px);
}
.content.disable .column{
  opacity: 0.6;
  pointer-events: none;
}
.column select{
  width: 100%;
  height: 53px;
  border: none;
  outline: none;
  background: none;
  font-size: 19px;
}
.wrapper button{
  width: 100%;
  border: none;
  outline: none;
  color: #fff;
  cursor: pointer;
  font-size: 20px;
  padding: 17px 0;
  margin-top: 20px;
  border-radius: 5px;
  background: #4A98F7;
}
</style>

<template>
<q-drawer v-model="drawer1" show-if-above :mini="!drawer1 || miniState" @click.capture="drawerClick" :width="230" :breakpoint="500" class="gt-sm" :class="$q.dark.isActive ? 'bg-grey-9' : ''">
    <q-scroll-area class="fit fixed q-mini-drawer-hide bg-grey-1" :horizontal-thumb-style="{ opacity: 0 }" style=" margin-top: 150px;">
        <q-list padding>
            <q-item clickable v-ripple active>
                <q-item-section avatar>
                    <q-icon name="event" />
                </q-item-section>

                <q-item-section>
                    Attendance
                </q-item-section>
            </q-item>

        </q-list>
    </q-scroll-area>
    <q-img class="  absolute-top q-mini-drawer-hide" src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ_r21v_iCVLKaiKHBRY9PnRnYUmqvc6jA8SQ&s" style="height: 150px">
        <div class="absolute-bottom bg-transparent">
            <q-avatar size="56px" class="q-mb-sm">
                <img src="https://cdn.quasar.dev/img/avatar4.jpg">
            </q-avatar>
            <div class="text-weight-bold">{{ username }}</div>
            <div>Employee</div>
        </div>
    </q-img>
    <!--
          in this case, we use a button (can be anything)
          so that user can switch back
          to mini-mode
        -->
    <div class="q-mini-drawer-active absolute" :style="{ top: '255px', right: miniState ? '24px' : '-29px' }">
        <q-btn dense class="btn" unelevated color="indigo-14" icon="chevron_left" @click="toggleMiniState" />
    </div>
</q-drawer>
<div class="q-pa-md gt-sm ">

    <div class="row justify-end  q-mt-xs">
        <div class="col-2 self-center " style="text-align: end; margin-right: 25px;">

            <div @click="checkIn" v-show="checkinButton">
                <q-icon name="check_circle" color="green" size="sm" />&nbsp;<span class="q-mt-sm" style="cursor: pointer;">checkin</span></div>
            <div @click="checkOut" v-show="checkinButton==false">
                <q-icon name="check_circle" color="red" size="sm" />&nbsp;<span class="q-mt-sm" style="cursor: pointer;">checkout</span></div>
        </div>

        <div class="col-2 self-center ">
            <q-icon name='alarm' color="indigo-13" size="sm" />&nbsp;<span class="q-mt-sm " style="cursor: pointer;">request leave</span>
        </div>
        <div class="col-1  q-mt-md q-mb-sm text-start">
            <q-avatar size="35px" class="text-white bg-indigo-14 q-mb-md">{{ firstLetter }}</q-avatar>

        </div>
    </div>

</div>
<div class="q-pa-md row q-mt-md">
    <div class="col-6 col-md-3">
        <q-select dense outlined v-model="currentMonthName" :options="months" label="select month" class="text-overline" />
    </div>&nbsp;&nbsp;
    <div class="col-4 col-md-2">
        <q-select dense outlined v-model="currentYear" :options="options" label="year" class="text-overline" />
    </div>

</div>
<div class="q-pa-md row  justify-between">

    <div v-for="atten in attendance" v-show="attendance.length!=0" :class="miniState == true ?'col-md-3 col-12 q-mt-md':'col-md-5 col-12 q-mt-md'">
        <q-card flat class="my-card ">

            <q-card-section class=" text-dark " style="background-color: #d3ffcf;">

                <div class="row justify-between">
                    <div class="col-3"><span class="text-body text-weight-bolder">{{ atten.month }}&nbsp;{{ atten.date }}</span></div>
                    <div class="col-4"><span class="text-caption text-center" :style="atten.checkIn > '10:00 AM' ? { color: 'red' } : { color: 'black' }">
                            <q-icon name="logout" class="q-mb-xs" color="green-6" />{{ atten.checkIn }}</span></div>
                    <div class="col-4"><span class="text-caption " :style="atten.checkOut < '06:00 PM' ? { color: 'red' } : { color: 'black' }">
                            <q-icon name="logout" class="q-mb-xs" color="green-6" v-show="atten.checkOut!=''" />{{ atten.checkOut  }}</span></div>
                </div>

            </q-card-section>

            <q-badge :color="atten.checkOut>'1.00 PM'?'orange': isWeekend ? 'purple-9':'green-9'" floating class="q-pa-xs">{{ atten.checkOut>'1.00 PM'?'halfday': isWeekend ? 'weekend' : 'present' }}</q-badge>

        </q-card>
    </div>
</div>
<div class="row justify-center q-mt-md" v-show="attendance.length==0">
    <div class="col-md-12  self-center text-center">
        <img src="../assets/search-concept-illustration_114360-95.avif" height="160px" width="160px" class="lt-md">
        <img src="../assets/search-concept-illustration_114360-95.avif" height="250px" width="250px" class="gt-sm">
        <p class="text-caption text-grey-7">No user data are available</p>
    </div>
</div>
<q-page-sticky position="bottom-right" :offset="[18, 18]" class='lt-md' flat>
    <q-fab color="indigo-14" direction="up" class="custom-fab" padding="xs">
        <template v-slot:icon="{ opened }">
            <q-icon :class="{ 'example-fab-animate--hover': opened !== true }" name="keyboard_arrow_up" :size="xs" />
        </template>

        <template v-slot:active-icon="{ opened }">
            <q-icon :class="{ 'example-fab-animate': opened === true }" name="close" />
        </template>

        <q-fab-action padding="xs" external-label label-position="left" class="text-indigo-13 bg-white" @click="onClick" icon="alarm" label="request leave" />
        <q-fab-action padding="xs" external-label label-position="left" class="text-green bg-white" @click="checkIn" v-show="checkinButton" icon="check_circle" label="Checkin" />
        <q-fab-action padding="xs" external-label label-position="left" class="text-red bg-white" @click="checkOut" v-show="checkinButton==false" icon="check_circle" label="Checkout" />
    </q-fab>
</q-page-sticky>
</template>

<script>
import Swal from 'sweetalert2';
import axios from 'axios';
export default {
    name: "Attendance",
    data() {

        return {
            drawer1: false,
            miniState: false,
            username: '',
            model: null,
            firstLetter: '',
            checkinButton: '',
            months: ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"],
            currentMonthName: '',
            currentDateValue: '',
            currentYear: '',
            checkInTime: '',
            checkOutTime: 'N/A',
            attendance: [],
            options: [2023, 2024, 2025, 2026, 2027, 2028, 2029, 2030],
            userId: localStorage.getItem("userId"),

        }
    },
    mounted() {
        this.getAttendance();
        this.scheduleEndOfDayCheck();
        this.username = localStorage.getItem("username")
        this.firstLetter = this.username.charAt(0)
        const currentDate = new Date();
        const currentMonth = currentDate.getMonth();
        const currentDat = currentDate.getDate();
        this.currentYear = currentDate.getFullYear();
        this.currentDateValue = currentDat;
        console.log(this.currentDateValue);
        this.currentMonthName = this.months[currentMonth];
        console.log(this.currentMonthName);

    },
    computed: {
        isWeekend() {
            const dayOfWeek = new Date().getDay();
            return dayOfWeek === 0 || dayOfWeek === 6;
        }

    },
    methods: {
        toggleMiniState() {
            this.miniState = !this.miniState;
        },
        drawerClick(e) {
            // if in "mini" state and user
            // click on drawer, we switch it to "normal" mode
            if (miniState.value) {
                miniState.value = false

                // notice we have registered an event with capture flag;
                // we need to stop further propagation as this click is
                // intended for switching drawer to "normal" mode only
                e.stopPropagation()
            }
        },
        checkIn() {
            this.checkinButton = false
            this.checkInTime = new Date().toLocaleTimeString([], {
                hour: '2-digit',
                minute: '2-digit'
            });
            console.log(this.checkInTime);

            let attendanceData = {
                month: this.currentMonthName,
                date: this.currentDateValue,
                checkIn: this.checkInTime,
                checkOut: this.checkOutTime,
                employeeId: this.userId
            }

            axios.post('http://localhost:3000/attendance/addAttendance', attendanceData)
                .then(res => {

                    Swal.fire({
                        title: 'Success!',
                        text: 'checkin successfully.',
                        icon: 'success',
                        confirmButtonText: 'OK'
                    });
                    this.getAttendance();

                })
                .catch(error => {
                    // Extract the message from the server response if available
                    let message = 'There was an error in login: ' + (error.response ? error.response.data.message : error.message);

                    this.$q.notify({
                        type: 'negative',
                        message: message,
                        position: 'top'
                    });
                    console.error('There was an error creating the attendance record:', message);
                });
        },
        checkOut() {
            this.checkinButton = true;
            this.checkOutTime = new Date().toLocaleTimeString([], {
                hour: '2-digit',
                minute: '2-digit'
            });

            if (this.attendance.length === 0) {
                this.$q.notify({
                    type: 'negative',
                    message: 'No attendance record found to check out.',
                    position: 'top'
                });
                return;
            }

            axios.post(`http://localhost:3000/attendance/addCheckout/${this.attendance[this.attendance.length - 1]._id}`, {
                    checkOut: this.checkOutTime
                })
                .then(res => {
                    Swal.fire({
                        title: 'Success!',
                        text: 'Check-out successfully.',
                        icon: 'success',
                        confirmButtonText: 'OK'
                    });
                    this.getAttendance();
                })
                .catch(error => {
                    this.$q.notify({
                        type: 'negative',
                        message: 'There was an error during check-out: ' + error.message,
                        position: 'top'
                    });
                    console.error('There was an error during check-out:', error.message);
                });
        },
        getAttendance() {
            axios.get(`http://localhost:3000/attendance/getAttendanceByEmployee/${this.userId}`)
                .then(res => {
                    console.log('attendance:', this.attendance = res.data.data);
                    console.log("=======================>", this.attendance);
                    if (this.attendance.length > 0 && this.attendance[this.attendance.length - 1].checkOut === 'N/A') {
                        this.checkinButton = false
                        console.log("hii");
                    } else {
                        this.checkinButton = true
                    }
                })
        },
        scheduleEndOfDayCheck() {
            const now = new Date();
            const endOfDay = new Date();
            endOfDay.setHours(18, 0, 0, 0); // Set time to 6 PM

            if (now < endOfDay) {
                const timeUntilEndOfDay = endOfDay - now;
                setTimeout(this.checkEndOfDayAttendance, timeUntilEndOfDay);
            }
        },
        checkEndOfDayAttendance() {
            const currentDate = new Date();
            const currentMonth = currentDate.toLocaleString('default', {
                month: 'long'
            });
            const currentDay = currentDate.getDate();

            if (this.isWeekday) {
                axios.get(`http://localhost:3000/attendance/getAttendanceByEmployee/${this.userId}`)
                    .then(res => {
                        const attendance = res.data.data;
                        const todayAttendance = attendance.find(att => att.date === currentDay && att.month === currentMonth);

                        if (!todayAttendance) {
                            const attendanceData = {
                                month: currentMonth,
                                date: currentDay,
                                checkIn: 'N/A',
                                checkOut: 'N/A',
                                employeeId: this.userId
                            };

                            axios.post('http://localhost:3000/attendance/addAttendance', attendanceData)
                                .then(response => {
                                    console.log('No check-in found, recorded N/A');
                                })
                                .catch(error => {
                                    console.error('Error recording N/A attendance:', error.message);
                                });
                        }
                    })
                    .catch(error => {
                        console.error('Error fetching attendance:', error.message);
                    });
            }
        }

    }
}
</script>

<style scoped>
q-fab-action {
    width: 10px !important;
}

.example-fab-animate,
.q-fab:hover .example-fab-animate--hover {
    animation: example-fab-animate 0.82s cubic-bezier(.36, .07, .19, .97) both;
    transform: translate3d(0, 0, 0);
    backface-visibility: hidden;
    perspective: 1000px;
}

@keyframes example-fab-animate {

    10%,
    90% {
        transform: translate3d(-1px, 0, 0)
    }

    20%,
    80% {
        transform: translate3d(2px, 0, 0)
    }

    30%,
    50%,
    70% {
        transform: translate3d(-4px, 0, 0)
    }

    40%,
    60% {
        transform: translate3d(4px, 0, 0)
    }
}

.btn {
    border-top-right-radius: 50%;
    border-bottom-right-radius: 50%;
    padding: 10px;
}
</style>

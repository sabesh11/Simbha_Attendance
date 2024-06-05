<template>
<div class="q-pa-md gt-sm">
    <div class="row justify-end  q-mt-xs">
        <div class="col-2 self-center" style="text-align: end; margin-right: 25px;">

            <div @click="checkIn" v-show="checkinButton">
                <q-icon name="check_circle" color="green" size="sm" />&nbsp;<span class="q-mt-sm" style="cursor: pointer;">checkin</span></div>
            <div @click="checkOut" v-show="checkinButton==false">
                <q-icon name="check_circle" color="red" size="sm" />&nbsp;<span class="q-mt-sm" style="cursor: pointer;">checkout</span></div>
        </div>

        <div class="col-2 self-center">
            <q-icon name='alarm' color="indigo-13" size="sm" />&nbsp;<span class="q-mt-sm " style="cursor: pointer;">request leave</span>
        </div>
        <div class="col-1   q-mt-md q-mb-sm">
            <q-avatar size="28px" class="text-white bg-indigo-14 q-mb-md">{{ firstLetter }}</q-avatar>
            
        </div>
    </div>

</div>
<div class="q-pa-lg row  justify-around">
    <div class="col-md-4 col-12 q-mt-lg" v-for="atten in attendance" v-show="attendance.length!=0">
        <q-card flat class="my-card ">

            <q-card-section class=" text-dark " style="background-color: #d3ffcf;">

                <div class="row justify-around">
                    <div class="col-3"><span class="text-body text-weight-bolder">{{ atten.month }}&nbsp;{{ atten.date }}</span></div>
                    <div class="col-4"><span class="text-caption text-center" :style="atten.checkIn > '10:00 AM' ? { color: 'red' } : { color: 'black' }">
                            <q-icon name="logout" class="q-mb-xs" />{{ atten.checkIn }}</span></div>
                    <div class="col-4"><span class="text-caption " :style="atten.checkOut < '07:00 PM' ? { color: 'red' } : { color: 'black' }">
                            <q-icon name="logout" class="q-mb-xs"  v-show="atten.checkOut!=''" />{{ atten.checkOut  }}</span></div>
                </div>

            </q-card-section>

            <q-badge :color="atten.checkOut>'1.00 PM'?'orange':'green-9'" floating class="q-pa-xs">{{ atten.checkOut>'1.00 PM'?'halfday':'present' }}</q-badge>

        </q-card>
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
            username: '',
            firstLetter: '',
            checkinButton: '',
            months: ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"],
            currentMonthName: '',
            currentDateValue: '',
            checkInTime: '',
            checkOutTime: '',
            attendance: [],
            userId: localStorage.getItem("userId")
        }
    },
    mounted() {
        this.getAttendance();
        this.username = localStorage.getItem("username")
        this.firstLetter = this.username.charAt(0)
        const currentDate = new Date();
        const currentMonth = currentDate.getMonth();
        const currentDat = currentDate.getDate();
        this.currentDateValue = currentDat;
        console.log(this.currentDateValue);
        this.currentMonthName = this.months[currentMonth];
        console.log(this.currentMonthName);
       
    },
    methods: {
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

            axios.post(`http://localhost:3000/attendance/addCheckout/665f5a9d17a0f79fd0c7e271`, {
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
            axios.get("http://localhost:3000/attendance/getAttendanceByEmployee/" + this.userId)
                .then(res => {
                    console.log('attendance:', this.attendance = res.data.data);
                    console.log("=======================>",this.attendance);
                    if (this.attendance.length > 0 && this.attendance[this.attendance.length - 1].checkOut === '') {
            this.checkinButton = false
            console.log("hii");
        }else{
            this.checkinButton = true
        }
                })
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


</style>

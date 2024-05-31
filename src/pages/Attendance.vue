<template>
<div class="q-pa-lg row  q-mt-md">
    <div class="col-md-4 col-12">
        <q-card flat class="my-card ">

            <q-card-section class="bg-primary text-white q-pa-md">

                <div class="row justify-around">
                    <div class="col-3"><span class="text-body text-weight-bolder">{{ currentMonthName }}</span></div>
                    <div class="col-4"><span class="text-subtitle2 text-center">{{ checkInTime }}</span></div>
                    <div class="col-4"><span class="text-subtitle2">{{ checkOutTime }}</span></div>
                </div>

            </q-card-section>

            <q-badge color="orange" floating>present</q-badge>

        </q-card>
    </div>
</div>
<q-page-sticky position="bottom-right" :offset="[18, 18]" class='lt-md'>
    <q-fab color="deep-purple-10" direction="up" class="custom-fab" padding="xs">
        <template v-slot:icon="{ opened }">
            <q-icon :class="{ 'example-fab-animate--hover': opened !== true }" name="keyboard_arrow_up" :size="xs" />
        </template>

        <template v-slot:active-icon="{ opened }">
            <q-icon :class="{ 'example-fab-animate': opened === true }" name="close" />
        </template>

        <q-fab-action padding="xs" external-label label-position="left" class="text-indigo-13 bg-white" @click="onClick" icon="alarm" label="request leave" />
        <q-fab-action padding="xs" external-label label-position="left" class="text-red bg-white" @click="checkIn" v-show="checkinButton" icon="check_circle" label="Checkin" />
        <q-fab-action padding="xs" external-label label-position="left" class="text-red bg-white" @click="checkOut" v-show="checkinButton==false" icon="check_circle" label="Checkout" />
    </q-fab>
</q-page-sticky>
</template>

<script>
import Swal from 'sweetalert2';
export default {
    name: "Attendance",
    data() {
        return {
            checkinButton: true,
            months: ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"],
            currentMonthName: '',
            checkInTime: '',
            checkOutTime: '',
            attendance: []
        }
    },
    mounted() {
        const currentDate = new Date();
        const currentMonth = currentDate.getMonth();
        console.log(currentMonth)
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
            console.log(checkInTime);
            Swal.fire({
        title: 'Success!',
        text: 'checkin successfully.',
        icon: 'success',
        confirmButtonText: 'OK'
      });
            let attendanceData = {
                month: this.currentMonthName,
                checkIn: this.checkInTime,
                checkOut: this.checkOutTime
            }

            this.attendance.push(attendanceData)
        },
        checkOut() {
            this.checkinButton = true
            this.checkOutTime = new Date().toLocaleTimeString([], {
                hour: '2-digit',
                minute: '2-digit'
            });
            console.log(this.checkOutTime);
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

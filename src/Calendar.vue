<template>
    <div ref="tuiCalendar">
    </div>
</template>
<script>
import Calendar from 'tui-calendar';

const calendarEvents = [
    'afterRenderSchedule',
    'beforeCreateSchedule',
    'beforeDeleteSchedule',
    'beforeUpdateSchedule',
    'clickDayname',
    'clickSchedule',
    'clickTimezonesCollapseBtn'
];

const scheduleNeedProp = [
    'start',
    'category'
];

export default {
    name: 'ToastUICalendar',
    props: {
        calendars: {
            type: Array,
            default() {
                return [];
            }
        },
        schedules: {
            type: Array,
            default() {
                return [];
            },
            validator(value) {
                let notHave = false;
                value.forEach(schedule => {
                    notHave = scheduleNeedProp.some(prop => !schedule.hasOwnProperty(prop));
                });

                return !notHave;
            }
        },
        view: {
            type: String,
            default() {
                return 'week';
            }
        },
        taskView: {
            type: [Boolean, Array],
            default() {
                return true;
            }
        },
        scheduleView: {
            type: [Boolean, Array],
            default() {
                return true;
            }
        },
        theme: {
            type: Object,
            default() {
                return {};
            }
        },
        template: {
            type: Object,
            default() {
                return {};
            }
        },
        week: {
            type: Object,
            default() {
                return {};
            }
        },
        month: {
            type: Object,
            default() {
                return {};
            }
        },
        useCreationPopup: {
            type: Boolean,
            default() {
                return true;
            }
        },
        useDetailPopup: {
            type: Boolean,
            default() {
                return true;
            }
        },
        timezones: {
            type: Array,
            default() {
                return [];
            }
        },
        disableDblClick: {
            type: Boolean,
            default() {
                return false;
            }
        },
        isReadOnly: {
            type: Boolean,
            default() {
                return false;
            }
        }
    },
    watch: {
        calendars(newValue) {
            this.calendarInstance.setCalendars(newValue);
        },
        schedules() {
            this.calendarInstance.clear();
            this.reflectSchedules();
        },
        view(newValue) {
            this.calendarInstance.changeView(newValue, true);
        },
        taskView(newValue) {
            this.calendarInstance.setOptions({taskView: newValue});
        },
        scheduleView(newValue) {
            this.calendarInstance.setOptions({scheduleView: newValue});
        },
        theme: {
            handler(newValue) {
                this.calendarInstance.setTheme(newValue);
            },
            deep: true
        },
        week: {
            handler(newValue) {
                const silent = this.view !== 'week' && this.view !== 'day';
                this.calendarInstance.setOptions({week: newValue}, silent);
            },
            deep: true
        },
        month: {
            handler(newValue) {
                const silent = this.view !== 'month';
                this.calendarInstance.setOptions({month: newValue}, silent);
            },
            deep: true
        },
        timezones(newValue) {
            this.calendarInstance.setOptions({timezones: newValue});
        },
        disableDblClick(newValue) {
            this.calendarInstance.setOptions({disableDblClick: newValue});
        },
        isReadOnly(newValue) {
            this.calendarInstance.setOptions({isReadOnly: newValue});
        }
    },
    data() {
        return {
            calendarInstance: null
        };
    },
    mounted() {
        this.calendarInstance = new Calendar(this.$refs.tuiCalendar, {
            defaultView: this.view,
            taskView: this.taskView,
            scheduleView: this.scheduleView,
            theme: this.theme,
            template: this.template,
            week: this.week,
            month: this.month,
            calendars: this.calendars,
            useCreationPopup: this.useCreationPopup,
            useDetailPopup: this.useDetailPopup,
            timezones: this.timezones,
            disableDblClick: this.disableDblClick,
            isReadOnly: this.isReadOnly
        });
        this.addEventListeners();
        this.reflectSchedules();
    },
    destroyed() {
        calendarEvents.forEach(event => this.calendarInstance.off(event));
        this.calendarInstance.destroy();
    },
    methods: {
        addEventListeners() {
            calendarEvents.forEach(event => {
                this.calendarInstance.on(event, (...args) => this.$emit(event, ...args));
            });
        },
        reflectSchedules() {
            if (this.schedules.length > 0) {
                this.invoke('createSchedules', this.schedules);
            }
        },
        getRootElement() {
            return this.$refs.tuiCalendar;
        },
        invoke(methodName, ...args) {
            let result;
            if (this.calendarInstance[methodName]) {
                result = this.calendarInstance[methodName](...args);
            }

            return result;
        }
    }
};
</script>

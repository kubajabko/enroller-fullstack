<template>
    <div>
        <new-meeting-form @added="addNewMeeting($event)"></new-meeting-form>

        <span v-if="meetings.length == 0">
               Brak zaplanowanych spotkań.
           </span>
        <h3 v-else>
            Zaplanowane zajęcia ({{ meetings.length }})
        </h3>

        <meetings-list :meetings="meetings"
                       :username="username"
                       @attend="addMeetingParticipant($event)"
                       @unattend="removeMeetingParticipant($event)"
                       @delete="removeMeeting($event)"></meetings-list>
    </div>
</template>

<script>
    import NewMeetingForm from "./NewMeetingForm";
    import MeetingsList from "./MeetingsList";

    export default {
        components: {NewMeetingForm, MeetingsList},
        props: ['username'],
        data() {
            return {
                meetings: []
            };
        },
        methods: {
            addNewMeeting(meeting) {
                this.$http.post('meetings', meeting);
            },
            addMeetingParticipant(meeting) {
                this.$http.post(`meetings/${meeting.id}/participants`)
                    .then(response => meeting.participants.push(response.body));
            },
            removeMeetingParticipant(meeting) {
                this.$http.delete(`meetings/${meeting.id}/participants/disenrollme`)
                    .then(() => meeting.participants.splice(meeting.participants.map(p => p.login).indexOf(this.username), 1));
            },
            removeMeeting(meeting) {
                this.$http.delete(`meetings/${meeting.id}`).then(() => this.meetings.splice(this.meetings.indexOf(meeting), 1));
            }
        },
        mounted() {
            this.$http.get('meetings').then(response => {
                this.meetings = response.body;
            });
        }
    }
</script>

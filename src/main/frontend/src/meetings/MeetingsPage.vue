<template>
  <div>
    <new-meeting-form @added="addNewMeeting($event)"></new-meeting-form>

    <span v-if="meetings.length === 0">
               Brak zaplanowanych spotkań.
           </span>
    <h3 v-else>
      Zaplanowane zajęcia ({{ meetings.length }})
    </h3>

    <meetings-list :meetings="meetings"
                   :username="username"
                   @attend="addMeetingParticipant($event)"
                   @unattend="removeMeetingParticipant($event)"
                   @delete="deleteMeeting($event)"></meetings-list>
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
                this.$http.post('meetings', meeting)
                    .then(response => {
                        this.meetings.push(response.body);
                    });
                this.getMeetings();
            },
            addMeetingParticipant(meeting) {
                // meeting.participants.push(this.username);
                let url = 'meetings/' + meeting.id.toString() + '/participants';
                this.$http.post(url, {params: {login: this.username}}) //meeting.id - undefined?
                    .then(response => {
                        meeting.participants.push(response.body, this.username)
                    });
                this.getMeetings();
            },
            removeMeetingParticipant(meeting) {
                this.$http.delete('meetings/' + meeting.id.toString() + "/participants?login=", this.username) // //meeting.id - undefined?
                    .then(response => {
                        meeting.participants.splice(meeting.participants.indexOf(this.username), 1)
                    });
                meeting.participants.splice(meeting.participants.indexOf(this.username), 1);                this.getMeetings();
                this.getMeetings();
            },
            deleteMeeting(meeting) {
                this.$http.delete('meetings/' + meeting.id.toString())
                    .then(response => {
                        this.meetings.splice(this.meetings.indexOf(meeting), 1);
                    });
                this.meetings.splice(this.meetings.indexOf(meeting), 1);
                this.getMeetings();
            },
            getMeetings() {
                this.$http.get('meetings')
                    .then(response => {
                        this.meetings = response.body;
                    })
            }
        },
        mounted() {
            this.$nextTick(this.getMeetings());
        },
    }
</script>

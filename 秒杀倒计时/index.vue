<template>
  <div>
    <Spike :start-time="startTime" :end-time="endTime" />
  </div>
</template>
<script>
import moment from "moment";
import Spike from "./Spike";
export default {
  components: {
    Spike
  },
  data() {
    return {
      startTime: moment("2019-07-08 18:55:00"),
      endTime: moment("2019-07-08 19:40:00")
    };
  }
};
</script>

<template><div></div></template>
<script>

export default {
  created() {
    var FileSaver = require('file-saver');
    FileSaver.saveAs("http://localhost:2019/api/samples/asd", "zalupa.zip");
  }
}
</script>

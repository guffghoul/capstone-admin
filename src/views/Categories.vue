<template>
    <div>
        <base-header type="gradient-success" class="pb-6 pb-8 pt-5 pt-md-8">
            
        </base-header>

        <div class="container-fluid mt--7">
            <div class="row">
                <div class="col">
                    <projects-table-3 title="Create categories"></projects-table-3>
                </div>
            </div>
        </div>

    </div>
</template>
<script>
  import ProjectsTable3 from './Tables/ProjectsTable3.vue'
  export default {
    name: 'tables',
    components: {
      ProjectsTable3
    }
  };
</script>
<style></style>

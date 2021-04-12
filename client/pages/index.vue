<template>
  <div class="container">
    <!--    imported from client/components -->
    <Logo/>
    <!--    Imported from client/components/Test/One.vue notice One doesnt have a prefix Test -->
    <TestOne/>
    <!--    Imported from client/components/Test/TestTwo.vue notice TestTwo is prefixed with Test -->
    <TestTwo/>

    <!--      No Errors because this is imported     -->
    <ImportedComponent/>
  </div>
</template>

<script lang="ts">
// can't ctrl click on the link if directory starts with ~/
// Cannot find module '~/components/test/importedComponent.vue' or its corresponding type declarations.
// But this is working perfectly and being displayed
import ImportedComponent from '~/components/test/importedComponent.vue'

// import ImportedComponent from '@/components/test/importedComponent.vue' // <--------- can be ctrl clicked
// import ImportedComponent from '../components/test/importedComponent.vue' // <--------- can be ctrl clicked

export default {
  components: { ImportedComponent },
}
</script>

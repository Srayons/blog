<template>
  <v-dialog
      transition="dialog-top-transition"
      max-width="600"
  >
    <template v-slot:activator="{ on, attrs }">
      <v-btn
          color="primary"
          v-bind="attrs"
          v-on="on"
      >From the top</v-btn>
    </template>
    <template v-slot:default="isOpenChatModel">
      <v-card>
        <v-toolbar
            color="primary"
            dark
        >Opening from the top</v-toolbar>
        <v-card-text>
          <div class="text-h2 pa-12">Hello world!</div>
        </v-card-text>
        <v-card-actions class="justify-end">
          <v-btn
              text
              @click="isOpenChatModel.value = false"
          >Close</v-btn>
        </v-card-actions>
      </v-card>
    </template>
  </v-dialog>
</template>
<script>
export default {
  props: ['isOpenChatModel']
}
</script>
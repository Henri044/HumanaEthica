<template v-if="theme">
  <v-dialog
      :value="dialog"
      @input="$emit('close-dialog')"
      @keydown.esc="$emit('close-dialog')"
      max-width="75%"
      max-height="80%"
  >
    <v-card>
      <v-form ref="form" v-model="valid" lazy-validation>
        <v-card-title>
          <span class="headline">Associate Theme</span>
        </v-card-title>

        <v-select
            v-model="theme"
            label="Theme"
            :items="themes"
            return-object
            item-text="name"
            item-value="name"
            required
        />

        <v-card-actions>
          <v-spacer />
          <v-btn
              color="blue darken-1"
              @click="$emit('close-dialog')"
              data-cy="cancelButton"
          >Close</v-btn
          >
          <v-btn color="blue darken-1" @click="submit" data-cy="saveButton"
          >Add</v-btn
          >
        </v-card-actions>
      </v-form>
    </v-card>
  </v-dialog>
</template>

<script lang="ts">
import { Component, Model, Prop, Vue } from 'vue-property-decorator';
import RemoteServices from '@/services/RemoteServices';
import Theme from '@/models/theme/Theme';
@Component({
  components: {},
})
export default class AssociateThemeView extends Vue {
  @Model('dialog', Boolean) dialog!: boolean;
  valid = true;
  success = false;
  themes: Theme[] | [] = [];
  theme: Theme = new Theme();

  async created() {
    this.themes = await RemoteServices.getThemesAvailableforInstitution();
  }

  async submit() {
    this.success = false;

    if (!(this.$refs.form as Vue & { validate: () => boolean }).validate())
      return;

    try {
      await RemoteServices.addThemetoInstitution(
        this.theme
      );
      this.success = true;
      await this.$router.push({ name: 'home' });
    } catch (error) {
      await this.$store.dispatch('error', error);
    }
  }
}
</script>

<style scoped>
.add-theme-feedback-container {
  height: 25px;
}
.add-theme-feedback {
  font-size: 1.05rem;
  color: #1b5e20;
  text-transform: uppercase;
}
</style>

<script>
import ResourceTable from '@shell/components/ResourceTable';
import { EPINIO_TYPES } from '../types';
import Loading from '@shell/components/Loading';

export default {
  name:       'EpinioConfigurationsList',
  components: {
    Loading,
    ResourceTable,
  },
  async fetch() {
    this.$store.dispatch(`epinio/findAll`, { type: EPINIO_TYPES.APP });
    this.$store.dispatch(`epinio/findAll`, { type: EPINIO_TYPES.SERVICE_INSTANCE });
    await this.$store.dispatch(`epinio/findAll`, { type: EPINIO_TYPES.CONFIGURATION });
  },
  props:      {
    schema: {
      type:     Object,
      required: true,
    },
  },

  computed: {
    rows() {
      return this.$store.getters['epinio/all'](EPINIO_TYPES.CONFIGURATION);
    },
  }
};
</script>

<template>
  <Loading v-if="$fetchState.pending" />
  <div v-else>
    <ResourceTable
      v-bind="$attrs"
      :rows="rows"
      :schema="schema"
      v-on="$listeners"
    >
      <template #cell:service="{ row }">
        <LinkDetail
          v-if="row.service"
          :key="row.service.id"
          :row="row.service"
          :value="row.service.meta.name"
        />
        <span
          v-else
          class="text-muted"
        >&nbsp;</span>
      </template>
      <template #cell:boundApps="{ row }">
        <span v-if="row.applications.length">
          <template v-for="(app, index) in row.applications">
            <LinkDetail
              :key="app.id"
              :row="app"
              :value="app.meta.name"
            />
            <span
              v-if="index < row.applications.length - 1"
              :key="app.id + 'i'"
            >, </span>
          </template>
        </span>
        <span
          v-else
          class="text-muted"
        >&nbsp;</span>
      </template>
    </ResourceTable>
  </div>
</template>

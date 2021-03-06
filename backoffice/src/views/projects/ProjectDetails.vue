<template>
  <section class="section form-section">
    <card class="top-image-card">
      <template v-slot:header>
        Details
      </template>

      <form-wrapper
        ref="formWrapper"
        @submit="handleSubmit"
      >

        <form-generator
          ref="formGenerator"
          :value.sync="formData"
          :schema="projectDetailsSchema" />

        <template v-slot:buttons>

          <crud-buttons
            :loading="loading"
            :show-delete="true"
            @reset="resetForm"
            @remove="handleRemove" />

        </template>
      </form-wrapper>
    </card>
  </section>
</template>

<script>
import Vue from 'vue'
import Card from '@/components/ui/Card.vue'
import CrudButtons from '@/components/form/CrudButtons.vue'
import FormGenerator from '@/components/form/FormGenerator.vue'
import FormWrapper from '@/components/form/FormWrapper.vue'
import projectDetailsSchema from '@/helpers/schemas/projects/projectDetailsSchema.js'

export default {
  name: 'ProjectDetail',
  props: {
    data: {
      required: true,
      validate: (value) => ['object', 'undefined'].find(typeof value)
    },
    meta: {
      type: [Array, Object],
      required: false
    },
    loading: {
      type: Boolean,
      default: false
    },
    update: {
      type: Function,
      required: true
    },
    read: {
      type: Function,
      required: true
    },
    remove: {
      type: Function,
      required: true
    }
  },
  components: {
    Card,
    CrudButtons,
    FormGenerator,
    FormWrapper
  },
  data () {
    return {
      projectDetailsSchema: projectDetailsSchema,
      formData: this.data || {}
    }
  },
  mounted () {
    if (!this.data || !this.meta) {
      this.read()
        .then(response => {
          this.updateFormData(response.data)
        })
    }
  },
  methods: {
    handleSubmit () {
      Vue.nextTick(() => {
        this.update(this.formData)
          .then(() => this.handleSuccess())
      })
    },
    resetForm () {
      this.read()
        .then(response => this.updateFormData(response.data))
    },
    saveChanges () {
      this.update(this.items)
    },
    updateFormData (formData) {
      Object.keys(formData).map(key => {
        this.$set(this.formData, key, formData[key])
      })
    },
    handleRemove () {
      this.remove()
        .then(() => {
          this.$router.push({ name: 'projects' })
        })
    },
    handleSuccess () {
      this.$buefy.toast.open({
        message: 'Product details updated!',
        type: 'is-primary'
      })
    }
  }
}
</script>

<style lang="scss" scoped>
  .form-cards-container {
    .card-container {
      margin-bottom: 2rem;
    }
  }
</style>

<template lang="pug">
  div.resource
    span(v-if="!edit" @click="toggleEdit") {{resource.name}} {{resource.location}} {{resource.website}} 
      span (
      span(v-for="skill, index in mappedSkills")
        span(v-if="index") , 
        span {{skill.name}}
      span )
    form(
      v-if="edit"
      @keydown.enter.prevent="updateResource"
      @keydown.esc.prevent="toggleEdit"
    )
      p
        label Name
        input(v-model="pResource.name")

      p
        label Location
        input(v-model="pResource.location")

      p
        label Website
        input(v-model="pResource.website")

      ul
        li(v-for="skill in skillsArray")
          label
            input(
              type="checkbox"
              v-bind:value="skill.id"
              v-model="pResource.skills"
            )
            span {{skill.name}}

      p.error(v-for="error in errors") {{error}}
      
      p
        button(@click.prevent="toggleEdit") Cancel
        button(@click.prevent="updateResource") Save
</template>

<script>
  import Resource from 'models/resource';
  import { database } from 'config/firebase';

  export default {
    data() {
      return {
        edit: false,
        pResource: new Resource(this.resource),
        errors: [],
      };
    },

    props: {
      resource: Object,
    },

    computed: {
      mappedSkills() {
        return (this.resource.skills)
          ? this.resource.skills.map(skillId => this.skills[skillId])
          : null;
      },
    },

    methods: {
      toggleEdit() {
        this.edit = !this.edit;
      },

      updateResource() {
        this.pResource.set()
          .then(() => {
            this.toggleEdit();
          })
          .catch((err) => { throw err; });
      },
    },

    firebase: {
      skills: {
        source: database.ref('/skills'),
        asObject: true,
      },
      skillsArray: database.ref('/skills'),
    },

    vuex: {
      actions: {
        dispatchUpdateResource({ dispatch }) {
          dispatch('UPDATE_RESOURCE', {
            resource: this.pResource,
          });
        },
      },
    },
  };
</script>
<template>
  <q-page :padding="true">
    <q-btn v-if="isAuthenticated" style="margin-bottom: 4px;" label="New Topic" @click="$router.replace('/topicedit')" />

        <q-table
          title="Topics"
          :data="rootConversations"
          :columns="columns"
        >
       <template slot="body" slot-scope="props">
        <q-tr :props="props">
          <q-td  key="type" style="width: 30px"  :props="props">
            <img  src="statics/images/cogwheel_sm.png" >
          </q-td>
          <q-td key="label" :props="props">
            {{props.row.label}}
          </q-td>
          <q-td key="handle" :props="props">
            {{props.row.handle}}
          </q-td>
          <q-td key="date" :props="props">
            {{props.row.date}}
          </q-td>
          <q-td key="nodeId" auto-width :props="props">
            <router-link :to="{ name: 'topicview', params: { id:  props.row.nodeId }}">View</router-link>
          </q-td>
        </q-tr>
        </template>


            </q-table>
  </q-page>
</template>

<script>
//  import api from 'src/api'
//  const conversation = api.service('conversation')
  import { mapActions, mapGetters } from "vuex";

  export default {
    props: ['user'],
    data() {
      return {
        columns: [
          {
            name: 'type',
            required: true,
            label: '',
            align: 'left',
            field: 'type',
            sortable: true
          },
          {
            name: 'label',
            required: true,
            label: 'Label',
            align: 'left',
            field: 'label',
            sortable: true
          },
          {
            name: 'handle',
            required: true,
            label: 'Handle',
            align: 'left',
            field: 'handle',
            sortable: true
          },
          {
            name: 'date',
            required: true,
            label: 'Date',
            align: 'left',
            field: 'date',
            sortable: true
          },
          {
            name: 'nodeId',
            required: true,
            label: 'Action',
            align: 'left',
            field: 'nodeId',
            sortable: true
          },
        ],
        isAuthenticated: false,
        serverPagination: {},
        serverData: []
      }
    },
    methods: {
      ...mapActions("conversation", { findConversations: "find" }),
      fill(n) {
        var jsx = {}
        jsx.imgsm = n.imgsm
        jsx.label = `<router-link :to="{ name: 'questview', params: { id: ${ n.id }}">${ n.label }</router-link>`
        jsx.creator = n.creator
        jsx.handle = n.handle
        jsx.date = n.date
        // console.info('JSX', jsx)
        this.$data.serverData.push(jsx)
      }
    },
    computed: {
      ...mapGetters("conversation", { allConversations: "list" }),
      rootConversations() {
        return this.allConversations.filter(c => c.type === "topic");
      }
    },
    async mounted() {
      this.$data.isAuthenticated = this.$store.getters.isAuthenticated;

      const query = {
        type: "topic",
        $limit: 1000
      };
      const conversations = await this.findConversations({ query });
      console.log("Query returned", { conversations });
      // this.fill(conversations);
      //turn off conversation tree
      this.$store.commit('questView', false)

    }
  }
</script>

<style>
</style>
<template>
    <div class="grid config-editor">
        <breadcrumbs class="navigation" :items="views" @on-select="selectCurrentView"></breadcrumbs>
        <div class="controls">
            <x-button @click="addVhost" type="success">
                <i class="fa fa-magic" aria-hidden="true"></i> VH
            </x-button>
            <x-button @click="reload">
                <i class="fa fa-refresh" aria-hidden="true"></i>
            </x-button>
        </div>

        <div class="divider">
            <hr>
        </div>

        <httpd-config-viewer class="directives" :config="currentView" @on-raw="onRaw"></httpd-config-viewer>
    </div>
</template>

<script>
  import {mapMutations, mapGetters, mapActions} from 'vuex'
  import {MUTATION_TYPE, GETTER_TYPE, ACTION_TYPE} from '@/utils/types'
  import HttpdConfigViewer from '@/components/HttpdConfigViewer'
  import Breadcrumbs from '@/components/Breadcrumbs'
  import XButton from '@/components/XButton'

  export default {
    components: {HttpdConfigViewer, Breadcrumbs, XButton},
    props: {
      views: {
        type: Array,
        default () {
          return []
        }
      },
      currentView: {
        type: Object,
        default () {
          return {}
        }
      }
    },
    data () {
      return {}
    },
    computed: {
      ...mapGetters('Settings', [
        GETTER_TYPE.Settings.xamppVirtualHostsFilePath
      ])
    },
    methods: {
      ...mapActions('Files', [
        ACTION_TYPE.Files.loadHttpdFile
      ]),
      ...mapMutations('Files', [
        MUTATION_TYPE.Files.setCurrentView
      ]),
      selectCurrentView (view) {
        this.setCurrentView({ view })
      },
      addVhost () {
        this.$router.push('constructor')
      },
      reload () {
        this.loadHttpdFile(this.xamppVirtualHostsFilePath).then(() => {
          this.$message({
            type: 'success',
            title: 'Loaded'
          })
        }).catch(e => {
          this.$message({
            type: 'error',
            title: 'Can\'t load'
          })
        })
      },
      onRaw (view) {
        this.$emit('on-raw', view)
      }
    }
  }
</script>

<style lang="scss" scoped>
    .grid {
        display: grid;
        grid-template-columns: 1fr auto auto;
        grid-template-rows: auto auto 1fr;
        grid-template-areas:
            "navigation controls"
            "divider divider"
            "directives directives"
    }

    .config-editor {
        border-right: 1px solid #4c4c4c;
        padding: 4px;
        padding-right: 5px;

        .navigation {
            grid-area: navigation;
        }

        .controls {
            grid-area: controls;
        }

        .divider {
            grid-area: divider;
        }

        .directives {
            grid-area: directives;

            padding-right: 5px;
            overflow: auto;
        }
    }
</style>
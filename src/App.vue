<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Sparcs Logo"
          class="shrink mr-2"
          contain
          src="https://sparcs-public.s3.ap-northeast-2.amazonaws.com/SPARCS_Branding/1_SPARCS/SPARCS_white.png"
          transition="scale-transition"
          width="120"
        />
      </div>
      <v-spacer></v-spacer>
      <v-btn text v-text="'Reset List'" @click="reset_list"></v-btn>
      <v-btn text v-text="`Hello ${my_name}`"></v-btn>

    </v-app-bar>

    <v-main style="margin-bottom: 120px">
      <v-container>
        <v-list three-line>
          <template v-for="(item, index) in items">
            <v-subheader
                    v-if="item.header"
                    :key="item.header"
                    v-text="item.header"
            ></v-subheader>

            <v-divider
                    v-else-if="item.divider"
                    :key="index"
                    :inset="item.inset"
            ></v-divider>

            <v-list-item
                    v-else
                    :key="item.createdAt"
            >
              <v-list-item-avatar>
                <div :class=randcol(item.name) style="height: 40px; width: 40px; font-size: 20px; color: white; text-align: center; line-height: 40px">{{item.name[0]}}</div>
              </v-list-item-avatar>

              <v-list-item-content>
                <v-list-item-title v-html="item.title"></v-list-item-title>
                <v-list-item-subtitle v-html="item.subtitle"></v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </template>
        </v-list>

      </v-container>

    </v-main>
    <v-sheet
            color="blue darken-1"
            elevation="6"
            height="auto"
            width="100%"
            style="position: fixed; bottom: 0; padding: 8px 16px"
    >
      <div style="display: flex; flex-direction: row; align-items: center">
        <div style="width: 100%; margin-right: 16px">
          <v-text-field label="Title" hide-details="auto" dark v-model="title" v-on:keyup.enter="send" ref="title"></v-text-field>
          <v-text-field label="Subtitle" hide-details="auto" dark v-model="subtitle" v-on:keyup.enter="send"></v-text-field>
        </div>
        <v-btn
                class="mx-2"
                fab
                dark
                large
                color="pink"
                @click="send"
        >
          <v-icon dark>
            mdi-send
          </v-icon>
        </v-btn>
      </div>

    </v-sheet>
  </v-app>
</template>

<script>

export default {
  name: 'App',

  components: {
  },

  data: () => ({
    my_name: '',
    items: [
    ],
    title: '',
    subtitle: '',
  }),
  methods: {
    send(){
      if(this.title){
        this.$socket.emit('comments/add', {
          name: this.my_name,
          title: this.title,
          subtitle: this.subtitle
        })
      } else alert("Please enter title");
    },
    randcol(str){
      let num=0;
      for(let i=0;i<str.length;i++) num+=str.charCodeAt(i);
      return ['red', 'orange', 'blue', 'green', 'purple', 'indigo', 'pink', 'grey'][num % 8];
    },
    reset_list(){
      this.$socket.emit('comments/delete')
    },
  },
  mounted(){
    this.$axios.get('https://randomuser.me/api/?nat=us')
    .then(res => {
      this.my_name = res.data.results[0].name.first;
    })

    this.$socket.on('comments', data => {
      this.items = data
    })
  }
};
</script>

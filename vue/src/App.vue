<template>
      <div id="app">
          <Notebook @change-page="changePage" @new-page="newPage" :pages="pages" :activePage="index" />
          <Page @save-page="savePage" @delete-page="deletePage" :page="pages[index]" />
      </div>
    </template>

    <script>
    import Notebook from './components/Notebook'
    import Page from './components/Page'
    import Firebase from 'firebase/app'
    //import 'firebase/auth'       // for authentication
    //import 'firebase/storage'    // for storage
    import 'firebase/database'   // for realtime database
    //import 'firebase/firestore'  // for cloud firestore
    //import 'firebase/messaging'  // for cloud messaging
    //import 'firebase/functions'  // for cloud functions

      var database = Firebase.initializeApp({
      apiKey: "AIzaSyCOvHxFLYsbHclUwuEo1sfA77b8HGW6VxU",
      authDomain: "neftekamsk-71979.firebaseapp.com",
      databaseURL: "https://neftekamsk-71979.firebaseio.com",
      projectId: "neftekamsk-71979",
      storageBucket: "neftekamsk-71979.appspot.com",
      messagingSenderId: "1091543827699"
  }).database().ref();

    export default {
      name: 'app',
      components: {
        Notebook,
        Page
      },
      data: () => ({
        pages: [],
        index: 0
      }),
      mounted() {
        database.once('value', (pages) => {
          pages.forEach((page) => {
            this.pages.push({
              ref: page.ref,
              title: page.child('title').val(),
              content: page.child('content').val()
            })
          })
        })
      },
      methods: {
        newPage () {
          this.pages.push({
            title: '',
            content: ''
          })
          this.index = this.pages.length - 1
        },
        changePage (index) {
          this.index = index
        },
        savePage () {
          var page = this.pages[this.index]
          if (page.ref) {
            this.updateExistingPage(page)
          } else {
            this.insertNewPage(page)
          }
        },
        updateExistingPage (page) {
          page.ref.set({
            title: page.title,
            content: page.content
          })
        },
        insertNewPage (page) {
          page.ref = database.push(page)
        },
        deletePage () {
          var ref = this.pages[this.index].ref
          ref && ref.remove()
          this.pages.splice(this.index, 1)
          this.index = Math.max(this.index - 1, 0)
        }
      }
    }
    </script>

    <style>
    html, body, #app {
        height: 100%;
    }

    body {
        margin: 0;
    }

    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        display: flex;
        flex-direction: row;
    }
    </style>
	
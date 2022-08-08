<template>
  <v-app>
    <v-col>
      <v-data-table
          :headers="headers"
          :items="books"
          :items-per-page="15"
          class="elevation-1"
      >
        <template v-slot:item="row">
          <tr>
            <td>{{ row.item.name }}</td>
            <td>{{ row.item.author }}</td>
            <td>{{ row.item.year }}</td>
            <td>
              <v-btn class="mx-2"
                     fab
                     dark
                     x-small
                     color="error"
                     v-on:click="deleteBook(row.item)"
              >x
              </v-btn>
            </td>
          </tr>
        </template>
      </v-data-table>
      <v-col>
        <div style="width: 30%; margin: 2rem 0 auto auto">
          <v-text-field label="Book name" v-model="newBookName"></v-text-field>
          <v-text-field label="Author" v-model="newBookAuthor"></v-text-field>
          <v-text-field label="Year" v-model="newBookYear"></v-text-field>
          <div style="margin: 0 0 auto auto">
            <v-btn v-on:click="saveBook()">SAVE</v-btn>
          </div>
        </div>
      </v-col>
    </v-col>
  </v-app>
</template>

<script>

import axios from "axios";
import {Book} from "@/components/Book";

export default {
  name: 'CrudTable',
  data: () => ({
    host: process.env.VUE_APP_HSKCRDHOST,
    port: ":8030",
    newBookName: "",
    newBookAuthor: "",
    newBookYear: "",
    books: [{name: "scs", author: "sdcsd", year: "sdcdsc"}],
    headers: [
      {text: 'Book name', value: 'name', align: 'left', sortable: false},
      {text: 'Author', value: 'author'},
      {text: 'Year', value: 'year'}
    ]
  }),
  created() {
    this.loadBooks();
  },
  methods: {
    loadBooks() {
      axios.get("http://" + this.host + this.port + "/api/v1/books")
          .then(response => this.books = response.data)
    },
    saveBook() {
      if (this.newBookName && this.newBookAuthor) {
        const book = new Book(this.newBookName, this.newBookAuthor, this.newBookYear)
        axios.post("http://" + this.host + this.port + "/api/v1/books", book)
            .then(response => this.books = response.data)
      }
    },
    deleteBook(book) {
      axios.delete("http://" + this.host + this.port + "/api/v1/books", {data: book})
          .then(response => this.books = response.data)
    }
  }
}
</script>


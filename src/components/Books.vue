<template>
  <div class="container">
    <div class="card">
      <div class="heading">
        <h4 class="title">NYTimes Books</h4>
        <hr>
        <p>Select Category from list:</p>

      </div>
      <form @submit.prevent="fetchBook">
      <select v-model="selected">
        <option disabled value="">-</option>
        <option v-for="cat in dispayCategories" :key="cat.list_name_encoded" :value="cat.list_name_encoded">{{ cat.list_name }}</option>
        
      </select>
      
      <div class="search" v-if="selected != ''">
        <input type="text" class="form-control" placeholder="Search by title" v-model.lazy="userBook"/>
        
        
        
      </div>
      </form>
      <div v-if="responseSearch === true">
      <div class='card'  v-for="r in listComplete" :key="r.primary_isbn10">
        
        
        <a v-bind:href="r.gLink" target='blank'><h5><em>{{r.title}}</em></h5></a>
        <h6><u>Descripción</u></h6>
        <p>{{r.description}}</p>
        <h6><u>AUTHOR:</u>{{r.author}}</h6>
        
      </div>
      </div>
      <div class="content">
        
        <div v-if = "responseAvailable == true">
          <!-- <p><em>ESPACIO DESARROLLO</em></p>
          <hr>
          <p v-for="cat in dispayCategories" :key="cat.list_name">
            <i>{{cat.list_name}}</i>
          </p> -->
          <!-- <hr>
          <span>Selected: {{ selected }}</span>
          <hr>
          <p>Url para fetch: {{urlQuery}}</p> -->
          <!-- <hr>
          <h6 v-if = "responseSearch == true">Resultado de la categoria:</h6>
          <p v-for="bk in listUserBooks" :key="bk.primary_isbn10">
            <i>{{bk.title}}</i>
          </p> -->
          <!-- <hr>
          <p>Libro buscado: {{userBook}}</p>
          <hr>
          <h6 v-if = "responseSearch == true">Resultado de la busqueda:</h6>
          <p v-for="b in listComplete" :key="b.primary_isbn10">
            <i>{{b.title}}</i>
          </p> -->
          <!-- <hr>
          <h6 v-if = "responseSearch == true">Links Querys:</h6>
          <p v-for="l in listComplete" :key="l.primary_isbn10">
            <i>{{l.gLink}}</i>
          </p> -->
        </div>

        

      </div>
      
    </div>
  </div>
</template>

<script>
export default {
  name: "Books",
  
  data () {
    return {
      displayCategories: [],
      
      responseAvailable: false,
      
      selected: '',
      userBook: '',
      listUserBooks: [],
      listUserSearch:[],
      responseSearch: false,
      count: 0,
      urlQuery:'',
      linkQuerys: [],
      listComplete: []
    }
  },
  created() {
    fetch('https://api.nytimes.com/svc/books/v3/lists/names.json?api-key=wMrIxYjKdpTQq76wy7ngPAG1OD0VJy8j')
      .then(response => {
        if(response.ok) {
          return response.json()

        } else {
          alert("Server response " + response.status + ": " + response.statusText);
        }
      })
      .then(response => {
        let allList = response.results;
        let categories = [];
        
        for (let index = 0; index < 10; index++) {
          categories.push(allList[index]);
          
          
          
        }
        this.dispayCategories = categories;
        
        
        this.responseAvailable = true;
      });
      
  },
  methods: {
    fetchBook(){
      let apiUrl = 'https://api.nytimes.com/svc/books/v3/lists.json?api-key=wMrIxYjKdpTQq76wy7ngPAG1OD0VJy8j';
      let catSearch = this.selected;
      let url = `${apiUrl}&list=${catSearch}`;
      this.urlQuery = url;
      
      



      fetch(url)
        .then(response => {
          if(response.ok) {
            return response.json()

          } else {
            alert("Server response " + response.status + ": " + response.statusText);
          }
        })
        .then(response => {
          let allList = response.results;
          let bookList = [];
          allList.map(item => {
            bookList.push(item.book_details[0])
          })



          this.listUserBooks = bookList;
          this.responseSearch = true;
          //this.listUserSearch = bookList;
          this.listUserSearch = bookList.filter( book => {
            return book.title.includes(this.userBook.toUpperCase())
          });
          this.linkQuerys = this.listUserSearch.map(book => {
            let strBook = book.title.replace(/\s/g, '+');
            let strAuthor = book.author.replace(/\s/g, '+');
            let linkG = `https://www.google.com/search?q=${strBook}+${strAuthor}`;
            return linkG.toLowerCase();
          });
          this.listComplete = this.listUserSearch.map((book,index) => ({...book, gLink: this.linkQuerys[index]}));


      });
     },
     counter(){this.count++}
    }
  
  
};
</script>

<style scoped lang="scss">
.container {
  z-index: 1;
  margin: 36px auto;
  max-width: 826px;
  background-color: white;
}

.card {
  box-shadow: 0 2px 3px rgba(10, 10, 10, 0.1), 0 0 0 1px rgba(10, 10, 10, 0.1);
  padding: 24px;
}

.list {
  > li {
    &:not(:last-child) {
      margin-bottom: 18px;
    }
    > a {
      color: #0a5b8c;
      display: block;
      margin-bottom: 6px;
    }

    > span {
      color: rgba(#3b4242, 0.7);
      font-size: 12px;
    }
  }
}

.btn {
  color: #fff;
  cursor: pointer;
  background-color: #117a8b;
  border: 1px solid transparent;
  padding: 6px 12px;
  border-radius: 6px;
  transition: all 0.1s ease-in;
  &:hover {
    background-color: #138496;
    border-color: #117a8b;
  }
}

.heading {
  margin-bottom: 12px;

  .title {
    font-size: 18px;
    font-weight: 600;
  }
}
.search {
  margin-bottom: 24px;
  .form-control {
    background-color: transparent;
    border: none;
    border-bottom: 1px solid #ced4da;
    border-radius: 0;
    outline: 0;
    box-shadow: none;
    padding: 6px 0;
    width: 100%;
  }
}
</style>

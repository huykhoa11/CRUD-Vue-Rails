<template>
  <div class="home">

    <form @submit.prevent="createItem(items.length + 1)">
      <div class="form-group" >
        <label for="exampleInputEmail1">Email address</label>
        <input v-model="content" type="text" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp" placeholder="Inpy content">
        <p>{{content}}</p>
      </div>
      <button type="submit" class="btn btn-primary">Submit</button>
    </form>


    <!-- <div class="input-group mb-3" @submit.prevent="createItem">
      <input type="text" v-model="content" class="form-control" placeholder="Input content" aria-label="Recipient's username" aria-describedby="basic-addon2">
      <p>{{content}}</p>
      <div class="input-group-append">
        <button class="btn btn-outline-secondary" type="button">Add content</button>
      </div>
    </div> -->

    <table class="table table-hover">
    <thead>
      <tr>
        <th scope="col">id</th>
        <th scope="col">Content</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in items" :key="item.id" >
        <th scope="row">{{ item.id }}</th>
        <td>{{item.content}}</td>
      </tr>

    </tbody>
  </table>
    
  </div>
</template>


<script>
import {ref, reactive} from 'vue'

export default {
  setup() {
    var items = ref(null);
    const content = ref('')
    
    const fetchURL = 'http://localhost:3000/posts';

    fetch(fetchURL)
      .then(response => response.json())    
      .then(data => {
        items.value = data
        console.log(data)

    })
    .catch(error => console.error(error));

    const configFetch = (method, url, data) => {
      console.log(data, "data fetch")
      fetch(url, 
           {
             method: method,
             body: JSON.stringify(data),
             headers: {
                'Content-Type': 'application/json',
              },
           })
        .then(response => response.json())
        .then(result => {
          console.log('Success:', result);
          // items.value.push({id: result.id, content: result.content, updated_at: result.updated_at})
          items.value.push(result)
        })
        .catch((error) => {
          console.error('Error:', error);
        });
    }

    const createItem = (index) => {
      console.log(content, "content")
      configFetch("POST", "http://localhost:3000/posts", {id: index, content:content.value})
    }

    // console.log(items, "items") 

    return {items, content, createItem}
  },
}
</script>

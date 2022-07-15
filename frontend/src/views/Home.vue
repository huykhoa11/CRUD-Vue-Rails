<template>
  <div class="home">



    <div class="input-group mb-3">
      <input v-model="content" type="text" class="form-control" placeholder="Input content" aria-label="Recipient's username" aria-describedby="basic-addon2">
      <div class="input-group-append">
        <button class="btn btn-outline-success" type="button" @click="createItem">Add post</button>
      </div>
    </div>

    <br><br>
    <table class="table table-hover">
    <thead>
      <tr>
        <th scope="col">id</th>
        <th scope="col">Content</th>
        <th scope="col"></th>
        <th scope="col"></th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(item, index) in items" :key="item.id" :class="'item-'+[item.id]">
        <th scope="row">{{ index+1 }}</th>
        <td>{{item.content}}</td>
        <td><button type="button" class="btn btn-info" data-toggle="modal" data-target="#exampleModal" @click="getItem(item.id)">Edit</button>&nbsp; 
            <button type="button" class="btn btn-danger" @click="deleteItem(item.id)">Delete</button></td>
      </tr>

    </tbody>
  </table>
    
    <!-- Modal -->
    <div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">Edit item</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
            <form @submit.prevent="editItem(itemId)">
            <div class="modal-body">
            
                <div class="form-group" >
                  <input v-model="content" type="text" class="form-control" id="input2" aria-describedby="emailHelp" placeholder="Input content">
                </div>
                
              </div>
              <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                <button type="submit" class="btn btn-primary" data-dismiss="modal" @click="editItem(itemId)">Save changes</button>
              </div>

            </form>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import {ref, reactive} from 'vue'

export default {
  setup() {
    var items = ref(null);
    const content = ref('')
    var itemId = ref(null)
    
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

    const createItem = () => {
      console.log(content, "content")
      configFetch("POST", "http://localhost:3000/posts", {content:content.value})
      content.value = ''
    
    }

    const getItem = (index) => {
      itemId.value = index;
      var found = items.value.find(e => e.id === index);
      content.value = found.content;
    }

    
    const editItem = (index) => {
      const requestOptions = {
          method: 'PUT',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ content:content.value })
      };
      fetch('http://localhost:3000/posts/' + index, requestOptions)
      .then(response => response.json())
      .then(data => {
        items.value.find(e => e.id === index).content = content.value;
        content.value = ''
        console.log("update successfully")
      })
      .catch((error) => {
          console.error('Error:', error);
      });
    }

    const deleteItem = (index) => {
      fetch('http://localhost:3000/posts/' + index, { method: 'DELETE' })
      .then(() => {
        items.value.splice(index, 1)
        console.log(items, "items" )
        var deleteItem = document.querySelector(".item-" +index);
        if (deleteItem) {
          deleteItem.remove();
        }
      });

    }


    return {items, content, createItem, deleteItem, editItem, getItem, itemId}
  },
}
</script>

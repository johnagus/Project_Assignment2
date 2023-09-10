<template>
    <div class="container">
      <h1>Input Produk</h1>
  
      <!-- Form for adding a new car -->
      <div class="form">
        <label for="">Brand Produk : </label><br>
        <input v-model="newProduct.brand" type="text" placeholder="Brand" /><br>
        <label for="">Deskripsi : </label><br>
        <textarea v-model="newProduct.description" type="text" placeholder="Deskripsi"></textarea><br>
        <label for="">Jumlah Stock : </label><br>
        <input v-model="newProduct.qty" type="number" placeholder="Quantity" /><br>
        <label for="">Harga : </label><br>
        <input v-model="newProduct.price" type="number" placeholder="Price" /><br>

        <button @click="addProduct" class="add my-button my-primary-button">Add Product</button>
      </div>
  
      <h1>Daftar Produk</h1>
      <!-- Produk List -->
      <table>
        <thead>
          <tr>
            <th>Produk</th>
            <th>Deskripsi</th>
            <th>Quantity</th>
            <th>Harga</th>
            <th>Total Harga</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(product, index) in products" :key="index">
            <td>{{ product.brand }}</td>
            <td>{{ product.description }}</td>
            <td>{{ product.qty }}</td>
            <td>Rp. {{ product.price }}</td>
            <td>Rp. {{ product.totalprice }}</td>
            <td>
              <button @click="editProduct(index)" class="my-button my-primary-button">Edit</button>
              <button @click="deleteProduct(index)" class="my-button my-secondary-button">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
  
      <!-- Modal for Editing -->
      <div v-if="showEditModal" class="modal">
        <div class="modal-content larger-modal">
          <h2>Edit Produk</h2>
          <form @submit.prevent="updateProduct">
            <label for="">Brand Produk : </label><br>
            <input v-model="editedProduct.brand" type="text" placeholder="Brand" /><br>
            <label for="">Deskripsi : </label><br>
            <textarea v-model="editedProduct.description" type="text" placeholder="Deskripsi"></textarea><br>
            <label for="">Jumlah Stock : </label><br>
            <input v-model="editedProduct.qty" type="number" placeholder="Quantity" /><br>
            <label for="">Harga Pokok : </label><br>
            <input v-model="editedProduct.price" type="number" placeholder="Price" disabled/><br>
            <label for="">Total harga : </label><br>
            <input v-model="editedProduct.totalprice" type="number" placeholder="Price"  disabled/><br>
            <button type="submit" class="my-button my-primary-button">Save</button>
            <button @click="cancelEdit" class="my-button my-secondary-button">Cancel</button>
          </form>
        </div>
      </div>
    </div>
    
    
    

  </template>
  
  <script>
  export default {
    data() {
      return {
        newProduct: {
          brand: "",
          description: "",
          qty: "",
          price: 0,
        },
        products: [],
        showEditModal: false,
        editedProduct: {
          brand: "",
          description: "",
          qty: "",
          price: 0,
        },
        editedProductIndex: -1,
      };
    },
    mounted() {
      // menerima produk dari local storage
      const productsData = localStorage.getItem("products");
      this.products = productsData ? JSON.parse(productsData) : [];
    },
    computed: {
    calculatedPrice() {
      // Calculate the price based on qty and inputPrice
      return this.newProduct.qty * this.newProduct.price;
    },
    calculatedEditedPrice() {
      // Calculate the price based on qty and inputPrice
      return this.editedProduct.price / this.newProduct.qty;
    },
  },
    methods: {
      addProduct() {
        this.products.push({ ...this.newProduct, totalprice: this.calculatedPrice, });
        this.saveProducts();
        this.newProduct.brand = "";
        this.newProduct.description = "";
        this.newProduct.qty = "";
        this.newProduct.price = 0;
        this.newProduct.totalprice = 0;
      },
      editProduct(index) {
        this.showEditModal = true;
        this.editedProductIndex = index;
        this.editedProduct = { ...this.products[index] };
        this.saveProducts();
        this.editedProduct.calculatedPrice = this.calculatedEditedPrice();
      },
      updateProduct() {
  if (this.editedProductIndex >= 0) {
    const editedProduct = { ...this.editedProduct };

    // Check if the edited qty is different from the current qty
    if (editedProduct.qty !== this.products[this.editedProductIndex].qty) {
      // Calculate the factor to adjust the price
      const priceAdjustmentFactor = editedProduct.qty / this.products[this.editedProductIndex].qty;

      // Adjust the price based on the factor
      editedProduct.price *= priceAdjustmentFactor;
    }

    // Update the product in the products array
    this.products.splice(this.editedProductIndex, 1, editedProduct);

    // Save the updated products
    this.saveProducts();

    // Reset the edit modal and index
    this.showEditModal = false;
    this.editedProductIndex = -1;
    this.editedProduct = { brand: "", product: "", qty: "", price: 0, totalprice: 0 };
  }
},

      cancelEdit() {
        this.showEditModal = false;
        this.editedProductIndex = -1;
        this.editedProduct = { brand: "", product: "", qty:"", price: 0, totalprice: 0 };
      },
      deleteProduct(index) {
        if (confirm("Apakah anda yakin ingin menghapus produk ini ?")) {
          this.products.splice(index, 1);
          this.saveProducts();
        }
      },
      saveProducts() {
        // menyimpan produk ke local storage
        localStorage.setItem("products", JSON.stringify(this.products));
      },
    },
  };
  </script>
  
  <style scoped>
  .container {
    max-width: 850px;
    margin: 0 auto;
    padding: 20px;
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }
  
  table, th, td {
    border: 1px solid #ddd;
  }
  
  th, td {
    padding: 10px;
    text-align: left;
  }
  
  .modal {
    display: block;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.7);
    z-index: 999;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .modal h2{
    font-size: 20px;
    text-align: center;
  }

  .modal-content {
    background-color: #fff;
    padding: 20px;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  }

  .larger-modal{
  width: 50%; /* You can adjust this value to make the modal wider */
  height: 50%; /* You can adjust this value to make the modal taller */
  background-color: white; /* Set the background color for the modal content */
  padding: 20px; /* Add padding for the modal content */
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.2); 
  }
  .my-button {
  padding: 5px 10px;
  border: none;
  cursor: pointer;
  border-radius: 4px;
  font-weight: bold;
  text-align: center;
  text-decoration: none;
}

.add{
    margin-top: 10px !important;
    margin-left: 0px !important; 
}
.my-primary-button {
  background-color: #007bff; 
  color: white;
  margin: 5px;
}

.my-secondary-button {
  background-color: #dd1e1e; 
  color: white;
  margin: 5px;
}

.container h1{
    font-size: 30px;
    text-align: center;
}

textarea{
    width:  50%;
    border: 2px solid lightgray;
    border-radius: 5px;
}

input{
    width: 50%;
    border: 2px solid lightgray;
    border-radius: 5px;
}

  </style>

  
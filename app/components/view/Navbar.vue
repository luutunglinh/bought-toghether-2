<template>
  <div class="navbar">
    <Dropdown>
      <div class="navbar-img">
        <img :src="imgSrc" alt="Error image navbar" />
      </div>
      <template #overlay>
        <Menu>
          <MenuItem>
            <a @click="showModal">Profile</a>
            <Modal
              title="Profile"
              v-model:visible="visible"
              :ok-button-props="{ disabled: typeData }"
              :cancel-button-props="{ disabled: typeData }"
              @ok="handelOk"
              @cancel="handelCancel"
            >
              <div class="input-title">User Name</div>
              <Input size="small" v-model:value="computedUserName" disabled />
              <div class="input-title">Shop url</div>
              <Input size="small" v-model:value="userData.text_shop"  />
            </Modal>
          </MenuItem>
          <MenuItem>
            <a href="" @click.prevent="logout">Logout </a>
          </MenuItem>
        </Menu>
      </template>
    </Dropdown>
  </div>
</template>
<script>
import axios from "axios";
import { Dropdown, Menu, MenuItem, Modal, Input, notification } from "ant-design-vue";
export default {
  name: "Navbar",
  components: {
    Dropdown,
    Menu,
    MenuItem,
    Modal,
    Input,
	notification
  },
  props: {
    user_image: String,
  },
  async mounted() {
	 const storedDataShop = JSON.parse(localStorage.getItem("data_Shop"));
	if (storedDataShop){
		this.userData.text_shop = storedDataShop.stores[0].name + ".myshopify.com"
		console.log(this.userData.text_shop);
		this.searchShop()
	}else {
		console.log("Ban chua co shop dang ki");
	}
  },
  data() {
    return {
      imgSrc: "data:image/jpeg;base64," + this.user_image,
      visible: false,
      userData: {
        userName: "",
        shopUrl: "",
      },
      typeData: true,
      tempShop: "",
	  data: [],
    };
  },
  computed: {
    computedUserName() {
      this.userData.userName = window.app_settings.user_name;
      return this.userData.userName;
    },
  },
  watch: {
    "userData.text_shop": function (newVal, oldVal) {
      if (newVal !== oldVal) {
        this.typeData = false;
      } else {
        this.typeData = true;
      }
    },
  },
  methods: {
    logout() {
      localStorage.removeItem("isStore");
      localStorage.removeItem("data_Shop");
      const baseURL = window.location.origin;
      //window.location.href = "/web/session/logout";
      window.location.href= baseURL +"/web/login?redirect=http%3A%2F%2Fodoo.website%2Fdashboard%2Fstore"
    },
    showModal() {
      this.visible = true;
      this.typeData = true;
      this.tempShop = this.userData.text_shop;
    },
    handelCancel() {
      this.userData.text_shop = this.tempShop;
    },
    async handelOk() {
      this.visible = false;
	  console.log(this.userData.text_shop);
	  this.searchShop()
    },
	async searchShop() {
		let isLoad = false
		try {
        const url = "/sample-app/store-begin";
        await axios
          .post(url, {
            jsonrpc: "2.0",
            params: {
              store: this.userData.text_shop,
              user_id: window.app_settings.id,
            },
          })
          .then((response) => {
            console.log(response.data.result);
			if (response.data.result.stores && typeof(response.data.result.stores) !== "undefined"){
				this.data = response.data.result
				localStorage.setItem("data_Shop", JSON.stringify(this.data));
				notification.success({
                message: "Success",
                description: response.data.result.message,

              });
			} else {
				notification.error({
					message: "Error",
					description: response.data.result.message,
				});
			}
          });
      } catch (error) {
        console.log("Error:", error);
		
      }
	},
  },
};
</script>
<style lang="css" scoped>
.navbar {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  position: fixed;
  width: 100%;
  height: 58px;
  flex-shrink: 0;
  background-color: #ffffff;
  z-index: 10;
}

.navbar .navbar-img {
  width: 34px;
  height: 34px;
  border-radius: 34px;
  margin-right: 25px;
  overflow: hidden;
  cursor: pointer;
}

.navbar .navbar-img img {
  width: 100%;
  height: 100%;
}

.input-title {
  margin-bottom: 5px;
  margin-top: 10px;
}
</style>

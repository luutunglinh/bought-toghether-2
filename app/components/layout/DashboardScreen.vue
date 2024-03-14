<template>
  <div class="dashboard">
    <Sidebar :comp="activeComp" :hasStore="hasStore"></Sidebar>
    <Navbar :user_image="user_image"></Navbar>
    <div class="main">
      <component
        :is="activeComp"
        :data_Shop="data_Shop"
        :hasStore="hasStore"
      ></component>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import InstallationGuide from "../view/InstallationGuide.vue";
import Store from "../view/Store.vue";
import Navbar from "../view/Navbar.vue";
import Sidebar from "../view/Sidebar.vue";
import Settings from "../view/Settings.vue";
import Customization from "../view/Customization.vue";
import { Button, Input, Form, FormItem, notification } from "ant-design-vue";
import { ArrowRightOutlined } from "@ant-design/icons-vue";
export default {
  name: "Dashboard",
  components: {
    Sidebar,
    Navbar,
    Settings,
    Store,
    InstallationGuide,
    Customization,
    Button,
    Input,
    Form,
    FormItem,
    notification,
    ArrowRightOutlined,
  },

  data() {
    return {
      currentComponent: window.app_settings.name,
      user_name: window.app_settings.user_name,
      user_image: window.app_settings.user_image,
      pathName: window.location.pathname,
      stores: window.app_settings.stores,
      activeComp: "",
      formState: {
        shop_text: "",
      },
      data_Shop: [],
    };
  },
  mounted() {
    // const storedDataShop = JSON.parse(localStorage.getItem("data_Shop"));
    // if(storedDataShop){
    // 	this.data_Shop = storedDataShop.stores;
    // 	console.log(typeof(this.data_Shop));
    // }
  },
  methods: {
    setUrl() {
      if (this.hasStore) {
        switch ((this.pathName = window.location.pathname)) {
          case "/dashboard/store":
            this.activeComp = "store";
            break;
          case "/dashboard/customization":
            this.activeComp = "customization";
            break;
          case "/dashboard/installationGuide":
            this.activeComp = "installationGuide";
            break;
          default:
            if (this.pathName.includes("/dashboard/store/")) {
              const store_name = this.pathName.split("/dashboard/store/")[1];
              const exist_name = this.stores
                .map((item) => item.name)
                .includes(store_name);
              if (exist_name) {
                window.app_settings.store = store_name;
                this.activeComp = "Settings";
              }
            }
        }
      }
	//   else {
	// 	notification.warning({
    //       message: "Warning",
    //       description: "You need to integrate link shop url in profile ",
    //     });
	//   }
    },
    // async addShop() {
    //   console.log(this.formState.shop_text);
    //   try {
    //     const url = "/sample-app/store-begin";
    //     await axios
    //       .post(url, {
    //         jsonrpc: "2.0",
    //         params: {
    //           store: this.formState.shop_text,
    //           user_id: window.app_settings.id,
    //         },
    //       })
    //       .then((response) => {
    //         console.log(response.data);
    //         if (response.data.result.stores && typeof(response.data.result.stores) !== "undefined") {
    //           this.data_Shop = response.data.result.stores;
    //           // Đặt biến là true nếu có dữ liệu
    // 		  this.isStore = true;
    //           console.log(typeof this.data_Shop);
    //         //   localStorage.setItem("isStore", JSON.stringify(this.isStore));
    //         //   localStorage.setItem("data_Shop", JSON.stringify(this.data_Shop));
    //           notification.success({
    //             message: "Success",
    //             description: response.data.result.message,
    //           });

    //         } else {
    //           // Đặt biến là false nếu không có dữ liệu
    //           this.isStore = false;
    // 		  notification.error({
    //             message: "Error",
    //             description: response.data.result.message,
    //           });
    //           localStorage.removeItem("isStore");
    //           localStorage.removeItem("data_Shop");
    //         }
    //       });
    //   } catch (error) {
    //     console.log("Error:", error);
    //   }
    // },
  },
  created() {
    this.setUrl();
    window.addEventListener("click", () => {
      this.setUrl();
    });
  },
  computed: {
    hasStore() {
      return this.stores.length > 0;
    },
  },
};
</script>
<style lang="css" scoped>
.dashboard {
  width: 100%;
  min-height: 1003px;
  display: flex;
  flex-direction: row;
  background-color: #f4f4f4;
}

.dashboard .main {
  width: 100%;
  margin: 103px 25px 34px 301px;
}

h1 {
  margin-right: 10px;
}
</style>

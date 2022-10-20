<template>
    <v-navigation-drawer v-model="drawer" absolute color="grey lighten-3" mini-variant hide-overlay stateless>
        <v-avatar class="d-block text-center mx-auto mt-4" size="36">
            <v-btn icon to="/admin/" text exact>
                <v-icon>mdi-view-dashboard</v-icon>
            </v-btn>
        </v-avatar>
        <v-divider class="mx-3 my-5"></v-divider>
        <v-avatar v-for="(item, index) in items" :key="index" class="d-block text-center mx-auto mb-6" size="36">
            <v-btn icon :to="item.to" text>
                <v-tooltip right>
                    <template v-slot:activator="{ on, attrs }">
                        <v-icon
                        v-text="item.icon"
                        v-bind="attrs"
                        v-on="on"
                        ></v-icon>
                    </template>
                    <span v-text="item.title"></span>
                </v-tooltip>
            </v-btn>
        </v-avatar>
    </v-navigation-drawer>
</template>


<script>
export default {
    props: {
        drawer: {
            type: Boolean,
            default: true,
        },
    },
    data() {
        return {
            items: [
                {
                    icon: "mdi-account-cog",
                    title: "顧客管理",
                    to: "/admin/users",
                },
                {
                    icon: "mdi-application-cog",
                    title: "予約管理",
                    to: "/admin/reserves",
                },
                {
                    icon: "mdi-store-cog",
                    title: "店舗管理",
                    to: "/admin/stores",
                },
                {
                    icon: "mdi-percent-circle",
                    title: "割引管理",
                    to: "/admin/discounts",
                },
                {
                    icon: "mdi-cog",
                    title: "設定",
                    to: "/admin/settings",
                },
            ]
        }
    }
};
</script>
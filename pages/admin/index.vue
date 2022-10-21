<template>
    <v-app>
        <v-system-bar app>
            <v-spacer></v-spacer>
            <v-icon>mdi-square</v-icon>
            <v-icon>mdi-circle</v-icon>
            <v-icon>mdi-triangle</v-icon>
        </v-system-bar>
        <v-navigation-drawer v-model="drawer" app mini-variant hide-overlay>
            <adminSidebar :drawer="drawer" />
        </v-navigation-drawer>
        <v-app-bar app dense flat v-if="$vuetify.breakpoint.mdAndDown">
            <v-icon @click="drawer = true">
                mdi-menu
            </v-icon>
        </v-app-bar>
        <v-main>
            <v-container fluid style="height: 100%" class="d-flex align-center justify-center">
                <div class="text-center">
                    <v-icon class="text-h1">
                        mdi-hand-pointing-left
                    </v-icon>
                    <h3 class="text-h6">
                        機能を選択
                    </h3>
                    <p class="text-body-1">
                        サイドバーから機能を選択して画面を確認
                    </p>
                </div>
            </v-container>
        </v-main>
    </v-app>
</template>

<script>
export default {
    layout: "admin",
    head() {
        return {
            title: "ダッシュボード",
        };
    },
    data() {
        return {
            drawer: this.$vuetify.breakpoint.mdAndDown ? false : true,
            dialog: true,
        };
    },
    components: {
        adminSidebar: () => import("~/components/adminSidebar.vue"),
    },
};
</script>

<style scoped>

</style>
<template>
    <v-app>
        <v-system-bar app>
            <v-spacer></v-spacer>
            <v-icon>mdi-square</v-icon>
            <v-icon>mdi-circle</v-icon>
            <v-icon>mdi-triangle</v-icon>
        </v-system-bar>
        <v-app-bar app dense flat v-if="$vuetify.breakpoint.mdAndDown">
            <v-icon @click="drawer = true">
                mdi-menu
            </v-icon>
            <v-spacer></v-spacer>
            <v-icon @click="subdrawer = true">
                mdi-text-long
            </v-icon>
        </v-app-bar>
        <v-navigation-drawer width="320" v-model="drawer" app hide-overlay
            :style="$vuetify.breakpoint.mdAndDown ? 'width: 100%;' : ''">
            <adminSidebar :drawer="drawer" />
            <v-list class="pl-14">
                <v-list-item-group v-model="discount_id" two-line>
                    <v-subheader>
                        クーポン一覧
                    </v-subheader>
                    <v-list-item v-for="(item) in discounts" :key="item.id" :value="item.id" @click="select()">
                        <v-list-item-content>
                            <v-list-item-title v-text="item.name"></v-list-item-title>
                            <v-list-item-subtitle v-text="item.description"></v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>
                </v-list-item-group>
                <v-dialog v-model="dialog1" width="500">
                    <template v-slot:activator="{ on, attrs }">
                        <v-btn color="pink" fab dark absolute bottom right class="mb-14" v-bind="attrs" v-on="on">
                            <v-icon>mdi-plus</v-icon>
                        </v-btn>
                    </template>
                    <v-card>
                        <v-card-title>
                            <span>新規追加</span>
                        </v-card-title>
                        <v-card-text>
                            <v-text-field
                            v-model="dialog1_form.name"
                            :counter="10"
                            label="店舗名"
                            required></v-text-field>
                            <v-text-field
                            v-model="dialog1_form.description"
                            label="店舗詳細"
                            required></v-text-field>
                            <v-text-field
                            v-model="dialog1_form.usage_limit"
                            label="利用回数。-1なら制限なし"
                            required></v-text-field>
                            <v-text-field
                            v-model="dialog1_form.discounts.price"
                            label="割引額"
                            required></v-text-field>
                        </v-card-text>
                        <v-card-actions>
                            <v-btn color="primary" text @click="adddiscounts">
                                追加
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </v-dialog>
            </v-list>
        </v-navigation-drawer>
        <v-main>
            <v-container fluid style="height: 100%" v-if="discount_id">
                <v-subheader>
                    クリックして編集、Enterで保存
                </v-subheader>
                <v-list two-line>
                    <v-list-item>
                        <v-list-item-icon>
                            <v-icon color="indigo">
                                mdi-storefront
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-edit-dialog
                            :return-value.sync="discount.name"
                            @save="discount.name = $event"
                            >
                            <v-list-item-title>{{discount.name}}</v-list-item-title>
                            <v-list-item-subtitle>クーポン名</v-list-item-subtitle>
                            <template v-slot:input>
                                <v-text-field
                                v-model="discount.name"
                                label="編集"
                                single-line
                                counter
                                ></v-text-field>
                            </template>
                            </v-edit-dialog>
                        </v-list-item-content>
                    </v-list-item>
                    <v-divider inset></v-divider>
                    <v-list-item>
                        <v-list-item-icon>
                            <v-icon color="indigo">
                                mdi-storefront
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-edit-dialog
                            :return-value.sync="discount.description"
                            @save="discount.description = $event"
                            >
                            <v-list-item-title>{{discount.description}}</v-list-item-title>
                            <v-list-item-subtitle>クーポン名</v-list-item-subtitle>
                            <template v-slot:input>
                                <v-text-field
                                v-model="discount.description"
                                label="編集"
                                single-line
                                counter
                                ></v-text-field>
                            </template>
                            </v-edit-dialog>
                        </v-list-item-content>
                    </v-list-item>
                    <v-divider inset></v-divider>
                    <v-list-item>
                        <v-list-item-icon>
                            <v-icon color="indigo">
                                mdi-storefront
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-edit-dialog
                            :return-value.sync="discount.usage_limit"
                            @save="discount.usage_limit = $event"
                            >
                            <v-list-item-title>{{discount.usage_limit}}</v-list-item-title>
                            <v-list-item-subtitle>使用制限</v-list-item-subtitle>
                            <template v-slot:input>
                                <v-text-field
                                v-model="discount.usage_limit"
                                label="編集"
                                single-line
                                counter
                                ></v-text-field>
                            </template>
                            </v-edit-dialog>
                        </v-list-item-content>
                    </v-list-item>
                    <v-divider inset></v-divider>
                    <v-list-item>
                        <v-list-item-icon>
                            <v-icon color="indigo">
                                mdi-storefront
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-list-item-title>{{discount.discounts.price}}</v-list-item-title>
                            <v-list-item-subtitle>割引額</v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>
                    <v-divider inset></v-divider>
                </v-list>
            </v-container>
            <v-container fluid style="height: 100%" class="d-flex align-center justify-center" v-else>
                <div class="text-center">
                    <v-icon class="text-h1">
                        mdi-hand-pointing-left
                    </v-icon>
                    <h3 class="text-h6">
                        クーポンを選択
                    </h3>
                    <p class="text-body-1">
                        リストからクーポンを選択して詳細を閲覧
                    </p>
                </div>
            </v-container>
        </v-main>
    </v-app>
</template>

<script>

export default {
    layout: false,
    head() {
        return {
            title: "割引管理",
        };
    },
    methods: {
        scrollToBottom() {
            this.$nextTick(() => {
                this.$refs.scroller.scrollToBottom();
            });
            setTimeout(() => {
                this.$refs.scroller.scrollToBottom();
            }, 1000);
        },
        select() {
            if (this.$vuetify.breakpoint.mdAndDown) {
                this.drawer = false
                this.subdrawer = false
            }
        },
        deletediscount() {
            this.sid = this.discount_id
            this.discount_id = "";
            this.apidiscounts = this.apidiscounts.filter((discount) => discount.id !== this.sid)
        },
        adddiscounts() {
            this.apidiscounts.push({
                ...this.dialog1_form,
                id: `discount-${this.apidiscounts.length + 1}`,
            })
            this.dialog1 = false
            this.dialog1_form = {
                id: "",
                name: "",
                description: "",
                discounts: {
                    price: 0,
                    percent: 0
                },
                usage_limit: 0,
            }
        }
    },
    computed: {
        discounts() {
            return this.apidiscounts;
        },
        discount() {
            return this.apidiscounts.find((discount) => discount.id == this.discount_id);
        },
    },
    data() {
        return {
            drawer: true,
            subdrawer: true,
            discount_id: "",
            loading: true,
            dialog1: false,
            dialog1_form: {
                id: "",
                name: "",
                description: "",
                discounts: {
                    price: 0,
                    percent: null
                },
                usage_limit: 0,
            },
            apidiscounts: [
                {
                    id: "dis-1",
                    name: "クーポン1",
                    description: "店舗1のクーポン1",
                    discounts: {
                        price: 2000,
                        percent: null
                    },
                    usage_limit: 1,
                },
                {
                    id: "dis-2",
                    name: "クーポン2",
                    description: "クーポン2の説明",
                    discounts: {
                        price: 2000,
                        percent: null
                    },
                    usage_limit: null,
                },
                {
                    id: "dis-3",
                    name: "クーポン3",
                    description: "クーポン3の説明",
                    discounts: {
                        price: 2000,
                        percent: null
                    },
                    usage_limit: null,
                },
                {
                    id: "dis-4",
                    name: "クーポン4",
                    description: "クーポン4の説明",
                    discounts: {
                        price: 0,
                        percent: null
                    },
                    usage_limit: 1,
                },
                {
                    id: "dis-5",
                    name: "クーポン5",
                    description: "クーポン5の説明",
                    discounts: {
                        price: 150,
                        percent: null
                    },
                    usage_limit: 1,
                },
                {
                    id: "dis-6",
                    name: "クーポン6",
                    description: "クーポン6の説明",
                    discounts: {
                        price: 150,
                        percent: null
                    },
                    usage_limit: 1,
                },
            ]
        };
    },
    components: {
        adminSidebar: () => import("~/components/adminSidebar.vue"),
    },
};
</script>

<style scoped>
.scroller {
    height: 100%;
}
</style>
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
                <v-list-item-group v-model="setting_id" two-line>
                    <v-subheader>
                    </v-subheader>
                    <v-list-item v-for="(item) in settings" :key="item.id" :value="item.id" @click="select()">
                        <v-list-item-content>
                            <v-list-item-title v-text="item.name"></v-list-item-title>
                            <v-list-item-subtitle v-text="item.description"></v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>
                </v-list-item-group>
            </v-list>
        </v-navigation-drawer>
        <v-main>
            <v-container fluid style="height: 100%" v-if="setting_id">
                <v-list two-line>
                    <v-subheader>
                    クリックして編集、Enterで保存
                    </v-subheader>
                    <v-list-item v-for="(item, index) in forms[setting_id]" :key="index">
                        <v-list-item-content>
                            <v-edit-dialog
                            :return-value.sync="item.value"
                            @save="item.value = $event"
                            >
                            <v-list-item-title>{{item.value}}</v-list-item-title>
                            <v-list-item-subtitle>{{item.label}}</v-list-item-subtitle>
                                <template v-slot:input>
                                    <component
                                    :is="item.type"
                                    :label="item.label"
                                    :items="item.type === 'v-select'? item.options : null"
                                    v-model="item.value"
                                    v-if="item"
                                    />
                                </template>
                            </v-edit-dialog>
                        </v-list-item-content>
                    </v-list-item>
                </v-list>
            </v-container>
            <v-container fluid style="height: 100%" class="d-flex align-center justify-center" v-else>
                <div class="text-center">
                    <v-icon class="text-h1">
                        mdi-hand-pointing-left
                    </v-icon>
                    <h3 class="text-h6">
                        項目を選択
                    </h3>
                    <p class="text-body-1">
                        リストから項目を選択して設定
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
            title: "設定",
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
        deletesetting() {
            this.sid = this.setting_id
            this.setting_id = "";
            this.apisettings = this.apisettings.filter((setting) => setting.id !== this.sid)
        },
        addsettings() {
            this.apisettings.push({
                ...this.dialog1_form,
                id: `setting-${this.apisettings.length + 1}`,
            })
            this.dialog1 = false
            this.dialog1_form = {
                id: "",
                name: "",
                description: "",
                settings: {
                    price: 0,
                    percent: 0
                },
                usage_limit: 0,
            }
        }
    },
    computed: {
        settings() {
            return this.apisettings;
        },
        setting() {
            return this.apisettings.find((setting) => setting.id == this.setting_id);
        },
    },
    data() {
        return {
            drawer: true,
            subdrawer: true,
            setting_id: "",
            loading: true,
            apisettings: [
                {
                    id: "s1",
                    name: "全般設定",
                    description: "サイト名などの基本設定",
                },
                {
                    id: "s2",
                    name: "通知設定",
                    description: "各種メール送信設定",
                },
            ],
            forms: {
                s1: {
                    name: {
                        label: "サイト名",
                        type: "v-text-field",
                        value: "サイト名",
                    },
                    description: {
                        label: "サイトの説明",
                        type: "v-text-field",
                        value: "サイトの説明",
                    },
                    line_access_token: {
                        label: "LINEアクセストークン",
                        type: "v-text-field",
                        value: "dqwfqg3pm31209j-g2043mgm;wlmldpf3@m@qlnmgnpd",
                    },
                    line_secret_token: {
                        label: "LINEシークレットトークン",
                        type: "v-text-field",
                        value: "fpm2-0mmwlmgpofjm;samf;lkengpbgpi3wjgpqwoj3f@:qf;emg;hnklbnifdjpmefm3",
                    },
                    line_account_url: {
                        label: "LINEアカウントURL",
                        type: "v-text-field",
                        value: "https://line.me/XXXXXXX",
                    },
                    email_address: {
                        label: "メールアドレス",
                        type: "v-text-field",
                        value: "test@example.com",
                    },
                    stripe_publishable_key: {
                        label: "Stripe Publishable Key",
                        type: "v-text-field",
                        value: "pk_test_XXXXXXXXXXXXXXXXXXXXXXXX",
                    },
                    stripe_secret_key: {
                        label: "Stripe Secret Key",
                        type: "v-text-field",
                        value: "sk_test_XXXXXXXXXXXXXXXXXXXXXXXX",
                    },
                    paypay_apikey: {
                        label: "PayPay API Key",
                        type: "v-text-field",
                        value: "dwfwgqwggegwgewgqqwd",
                    },
                    paypay_secretkey: {
                        label: "PayPay Secret Key",
                        type: "v-text-field",
                        value: "gwhbtnras;ln@ponf98nvnglen;zmdcprnhrnekfnwpfs",
                    },
                    paypay_marchant_id: {
                        label: "PayPay Marchant ID",
                        type: "v-text-field",
                        value: "fe;lmwqpomfapomfqmefkwrngsndm;aflemgpsdgjsughrnfeyqwt",
                    },
                    interval: {
                        label: "予約間隔",
                        type: "v-select",
                        value: "15分間隔",
                        options: [
                            {
                                text: "15分間隔",
                                value: "15分間隔",
                            },
                            {
                                text: "30分間隔",
                                value: "30分間隔",
                            },
                            {
                                text: "1時間間隔",
                                value: "1時間間隔",
                            },
                        ],
                    },
                },
                s2: {
                    promotion: {
                        label: "プロモーション",
                        type: "v-select",
                        value: "受信する",
                        options: [
                            {
                                text: "受信する",
                                value: "受信する",
                            },
                            {
                                text: "受け取る",
                                value: "受け取る",
                            },
                            {
                                text: "購読する",
                                value: "購読する",
                            },
                            {
                                text: "受信しない",
                                value: "受信しない",
                            },
                        ],
                    },
                    reserved: {
                        label: "予約成功メール",
                        type: "v-select",
                        value: "受信する",
                        options: [
                            {
                                text: "受信する",
                                value: "受信する",
                            },
                            {
                                text: "受信しない",
                                value: "受信しない",
                            },
                        ],
                    },
                    paid: {
                        label: "支払い完了メール",
                        type: "v-select",
                        value: "受信する",
                        options: [
                            {
                                text: "受信する",
                                value: "受信する",
                            },
                            {
                                text: "受信しない",
                                value: "受信しない",
                            },
                        ],
                    },
                    reserved: {
                        label: "LINE受信メール",
                        type: "v-select",
                        value: "受信する",
                        options: [
                            {
                                text: "受信する",
                                value: "受信する",
                            },
                            {
                                text: "受信しない",
                                value: "受信しない",
                            },
                        ],
                    },
                }
            }
        };
    },
    components: {
        adminSidebar: () => import("~/components/adminSidebar.vue"),
        VTextField: () => import("vuetify/lib/components/VTextField"),
        VSelect: () => import("vuetify/lib/components/VSelect"),
    },
};
</script>

<style scoped>
.scroller {
    height: 100%;
}
</style>
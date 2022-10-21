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
                mdi-message
            </v-icon>
        </v-app-bar>
        <v-navigation-drawer width="320" v-model="drawer" app hide-overlay
            :style="$vuetify.breakpoint.mdAndDown ? 'width: 100%;' : ''">
            <adminSidebar :drawer="drawer" />
            <v-list class="pl-14">
                <v-list-item-group v-model="user_id" two-line>
                    <v-subheader>
                        顧客一覧
                    </v-subheader>
                    <v-list-item v-for="(item) in users" :key="item.id" :value="item.id" @click="select()">
                        <v-list-item-avatar>
                            <v-img :src="item.avatar"></v-img>
                        </v-list-item-avatar>
                        <v-list-item-content>
                            <v-list-item-title v-text="item.name"></v-list-item-title>
                            <v-list-item-subtitle v-text="item.latest"></v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>
                </v-list-item-group>
            </v-list>
        </v-navigation-drawer>
        <v-navigation-drawer width="350" v-model="subdrawer" app right v-show="user_id"
            :style="$vuetify.breakpoint.mdAndDown ? 'width: 100%;' : ''">
            <DynamicScroller :items="msgs" :min-item-size="54" class="scroller" ref="scroller"
                @resize="scrollToBottom()">
                <template v-slot="{ item, index, active }">
                    <DynamicScrollerItem :item="item" :active="active" :data-index="index"
                        :size-dependencies="[ item.message ]">
                        <v-list-item>
                            <v-list-item-avatar dense v-if="item.sender !== 'user-0'">
                                <v-img :src="item.avatar"></v-img>
                            </v-list-item-avatar>
                            <v-list-item-content>
                                <v-alert :color="item.sender !== 'user-0' ? 'success' : 'info'" text dense
                                    class="my-0 text-body-2" v-text="item.message"></v-alert>
                                <v-list-item-subtitle class="text-caption"
                                    v-text="`${item.time}${item.isRead && item.sender === 'user-0' ? '・既読' : ''}`">
                                </v-list-item-subtitle>
                            </v-list-item-content>
                            <v-list-item-avatar dense v-if="item.sender === 'user-0'">
                            </v-list-item-avatar>
                        </v-list-item>
                    </DynamicScrollerItem>
                </template>
            </DynamicScroller>
            <template v-slot:append>
                <div class="pa-2 pb-5 pb-sm-2">
                    <v-textarea background-color="grey lighten-3" dense flat auto-grow hide-details rounded rows="1"
                        append-outer-icon="mdi-send" v-model="message" required ref="textarea"
                        @click:append-outer="sendMsg()" @keydown.shift.enter="sendMsg()"
                        placeholder="Shift + Enter で送信"></v-textarea>
                </div>
            </template>
        </v-navigation-drawer>
        <v-main>
            <v-container fluid v-if="user_id">
                <v-list two-line>
                    <v-list-item>
                        <v-list-item-icon>
                            <v-icon color="indigo">
                                mdi-phone
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-list-item-title>{{user.phone}}</v-list-item-title>
                            <v-list-item-subtitle>電話番号</v-list-item-subtitle>
                        </v-list-item-content>
                        <v-list-item-icon>
                            <v-icon>mdi-message-text</v-icon>
                        </v-list-item-icon>
                    </v-list-item>
                    <v-divider inset></v-divider>
                    <v-list-item>
                        <v-list-item-icon>
                            <v-icon color="indigo">
                                mdi-email
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-list-item-title>{{user.email}}</v-list-item-title>
                            <v-list-item-subtitle>メールアドレス</v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>
                    <v-divider inset></v-divider>
                    <v-list-item>
                        <v-list-item-icon>
                            <v-icon color="indigo">
                                mdi-account-box-multiple
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-list-item-title>{{user.name}}</v-list-item-title>
                            <v-list-item-subtitle>氏名</v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>
                    <v-divider inset></v-divider>
                    <v-list-item>
                        <v-list-item-icon>
                            <v-icon color="indigo">
                                mdi-map-marker
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-list-item-title>{{user.address.city}}{{user.address.street}}</v-list-item-title>
                            <v-list-item-subtitle>{{user.address.zip}} {{user.address.country}} {{user.address.pref}}
                            </v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>
                    <v-divider inset></v-divider>
                    <v-list-item>
                        <v-list-item-icon>
                            <v-icon color="indigo">
                                mdi-email
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-list-item-title>{{user.line_id}}</v-list-item-title>
                            <v-list-item-subtitle>LINE USERID</v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>
                </v-list>
            </v-container>
            <v-container fluid v-else style="height: 100%" class="d-flex align-center justify-center">
                <div class="text-center">
                    <v-icon class="text-h1">
                        mdi-hand-pointing-left
                    </v-icon>
                    <h3 class="text-h6">
                        顧客を選択
                    </h3>
                    <p class="text-body-1">
                        リストから顧客を選択して詳細を閲覧
                    </p>
                </div>
            </v-container>
        </v-main>
    </v-app>
</template>

<script>
import { users, msgs } from "@/assets/api.js";
export default {
    layout: false,
    head() {
        return {
            title: "顧客管理",
        };
    },
    methods: {
        scrollToBottom() {
            this.$nextTick(() => {
                this.$refs.scroller.scrollToBottom();
            });
        },
        sendMsg() {
            if (this.message) {
                this.apiMsgs.push({
                    id: `msg-${this.msgs.length + 1}`,
                    sender: `user-0`,
                    receiver: this.user_id,
                    time: new Date().toLocaleTimeString().split(':').slice(0, 2).join(':'),
                    isRead: true,
                    message: this.message,
                }
                )
                this.message = "";
                this.$refs.textarea.reset();
                this.$refs.textarea.focus();
                this.scrollToBottom();
            }
        },
        select() {
            if (this.$vuetify.breakpoint.mdAndDown) {
                this.drawer = false
                this.subdrawer = false
            }
            this.scrollToBottom();
        }
    },
    computed: {
        users() {
            return this.apiUsers.filter((u) => u.id !== "user-0");
        },
        msgs() {
            let msgslist = [];
            if (this.user_id) {
                this.apiMsgs.forEach((msg) => {
                    if (msg.sender === this.user_id || msg.receiver === this.user_id) {
                        msgslist.push({
                            id: msg.id,
                            sender: msg.sender,
                            receiver: msg.receiver,
                            avatar: this.apiUsers.filter((user) => user.id === msg.sender).pop().avatar,
                            message: msg.message,
                            time: msg.time,
                            isRead: msg.isRead,
                        });
                    }
                });
            }
            return msgslist;
        },
        user() {
            if (this.user_id) {
                return this.apiUsers.filter((user) => user.id === this.user_id).pop();
            }
        },
    },
    components: {
        adminSidebar: () => import("~/components/adminSidebar.vue"),
    },
    data() {
        return {
            drawer: true,
            subdrawer: true,
            user_id: "",
            message: "",
            loading: true,
            apiUsers: [
                {
                    id: "user-0",
                    name: "管理者",
                    avatar: "https://cdn.vuetifyjs.com/images/lists/1.jpg",
                    email: "admin@example.com",
                    username: "admin",
                },
                {
                    id: "user-1",
                    name: "清水愛菜",
                    avatar: "https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/27.jpg",
                    email: "filthy@gmail.com",
                    line_id: "cdcf7d11-d005-431f-9289-983cde884300",
                    username: "結衣56",
                    address: {
                        country: "オランダ",
                        pref: "愛媛県",
                        city: "Apopka",
                        street: "869 結菜Drives",
                        zip: "821-7982",
                    },
                    phone: "257-624-6049",
                },
                {
                    id: "user-2",
                    name: "松本莉子",
                    avatar: "https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/775.jpg",
                    email: "Bicycle@gmail.com",
                    line_id: "89c157ee-6026-4afe-bc55-f304ae4060dc",
                    username: "結菜14",
                    address: {
                        country: "エルサルバドル",
                        pref: "福井県",
                        city: "Palm Coast",
                        street: "366 中村Estate",
                        zip: "097-5858",
                    },
                    phone: "092-512-7059",
                },
                {
                    id: "user-3",
                    name: "田中蓮",
                    avatar: "https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/445.jpg",
                    email: "lux@gmail.com",
                    line_id: "987457be-a8d6-41db-8970-8fe704afaeff",
                    username: "樹.林",
                    address: {
                        country: "エストニア",
                        pref: "宮崎県",
                        city: "Newport News",
                        street: "812 樹Club",
                        zip: "775-0242",
                    },
                    phone: "034-664-0564",
                },
                {
                    id: "user-4",
                    name: "松本陸斗",
                    avatar: "https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/1221.jpg",
                    email: "silver@gmail.com",
                    line_id: "b17d37b7-6f40-4145-94a8-374cdcf05777",
                    username: "結衣_井上",
                    address: {
                        country: "中国",
                        pref: "山梨県",
                        city: "Anaheim",
                        street: "23483 松本Mountains",
                        zip: "255-5804",
                    },
                    phone: "268-795-0663",
                },
                {
                    id: "user-5",
                    name: "高橋陸斗",
                    avatar: "https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/1167.jpg",
                    email: "Franc@gmail.com",
                    line_id: "fc6a522b-6858-4757-91f9-a4e78b6526b5",
                    username: "結菜_小林",
                    address: {
                        country: "グレナダ",
                        pref: "山形県",
                        city: "Springfield",
                        street: "96695 井上Fall",
                        zip: "264-2899",
                    },
                    phone: "728-121-3132",
                },
                {
                    id: "user-6",
                    name: "井上杏",
                    avatar: "https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/1010.jpg",
                    email: "backing@gmail.com",
                    line_id: "9d6d903a-ffaa-44b7-a9a4-a62e356719b2",
                    username: "太一.伊藤",
                    address: {
                        country: "スロバキア",
                        pref: "石川県",
                        city: "San Antonio",
                        street: "57329 愛菜Lakes",
                        zip: "161-8735",
                    },
                    phone: "097-743-7331",
                },
                {
                    id: "user-7",
                    name: "田中颯太",
                    avatar: "https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/1242.jpg",
                    email: "fixed@gmail.com",
                    line_id: "f1184896-09b9-4679-968b-29440e961c2e",
                    username: "陸斗_高橋14",
                    address: {
                        country: "アルゼンチン",
                        pref: "北海道",
                        city: "Georgetown",
                        street: "622 蒼空Harbors",
                        zip: "497-2997",
                    },
                    phone: "357-958-0304",
                },
                {
                    id: "user-8",
                    name: "渡辺結菜",
                    avatar: "https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/1003.jpg",
                    email: "male@gmail.com",
                    line_id: "85396716-2573-4191-a1cb-7e7713a092a7",
                    username: "心愛.清水96",
                    address: {
                        country: "ウルグアイ",
                        pref: "茨城県",
                        city: "Lake Elsinore",
                        street: "65262 颯太Pike",
                        zip: "401-5567",
                    },
                    phone: "004-190-3787",
                },
                {
                    id: "user-9",
                    name: "木村太一",
                    avatar: "https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/1144.jpg",
                    email: "Technician@gmail.com",
                    line_id: "5acc640b-5b72-484e-b5a2-139ecea9534b",
                    username: "蓮_林",
                    address: {
                        country: "セネガル",
                        pref: "和歌山県",
                        city: "Dubuque",
                        street: "80840 斎藤Courts",
                        zip: "834-4845",
                    },
                    phone: "181-463-6569",
                },
                {
                    id: "user-10",
                    name: "鈴木美咲",
                    avatar: "https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/822.jpg",
                    email: "Table@gmail.com",
                    line_id: "06378a7e-0375-441e-a998-ae52e613df16",
                    username: "蒼空97",
                    address: {
                        country: "フィリピン",
                        pref: "石川県",
                        city: "Newport News",
                        street: "0369 井上Freeway",
                        zip: "155-6478",
                    },
                    phone: "928-812-5029",
                },
                {
                    id: "user-11",
                    name: "鈴木翼",
                    avatar: "https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/909.jpg",
                    email: "deposit@gmail.com",
                    line_id: "fa3535bf-bc14-4dd7-9893-92655363f4b3",
                    username: "陽菜27",
                    address: {
                        country: "ブータン",
                        pref: "大阪府",
                        city: "Dale City",
                        street: "94656 清水Place",
                        zip: "841-2404",
                    },
                    phone: "126-136-9095",
                },
            ],
            apiMsgs: [
                {
                    id: "msg-0",
                    sender: "user-1",
                    receiver: "user-0",
                    time: "12:01",
                    isRead: false,
                    message: "窒息 しょうゆ しえんする 殺人者 やさい きょうふ あおい はなじ.",
                },
                {
                    id: "msg-1",
                    sender: "user-0",
                    receiver: "user-11",
                    time: "20:24",
                    isRead: true,
                    message: "たらす ほうげん みなと きげんご 合う 魔法 かっこう かくじっけん 黒板 じょうだん.",
                },
                {
                    id: "msg-2",
                    sender: "user-3",
                    receiver: "user-0",
                    time: "14:10",
                    isRead: false,
                    message: "おかね 宜しく はちのす 双 米国 壊す 左手 普及 かっこう 不思議.",
                },
                {
                    id: "msg-3",
                    sender: "user-0",
                    receiver: "user-10",
                    time: "11:01",
                    isRead: true,
                    message: "せいかん 人口 ちがい.",
                },
                {
                    id: "msg-4",
                    sender: "user-2",
                    receiver: "user-0",
                    time: "0:05",
                    isRead: false,
                    message: "なおさら 話 待合 はんそう 下さい 暴力 間隔 ひんかく.",
                },
                {
                    id: "msg-5",
                    sender: "user-0",
                    receiver: "user-1",
                    time: "10:30",
                    isRead: true,
                    message: "フランス人 じぎする 靖国神社.",
                },
                {
                    id: "msg-6",
                    sender: "user-9",
                    receiver: "user-0",
                    time: "14:54",
                    isRead: false,
                    message: "こくみん 胃 封筒 米兵 たくす ちかく.",
                },
                {
                    id: "msg-7",
                    sender: "user-0",
                    receiver: "user-1",
                    time: "21:41",
                    isRead: true,
                    message: "迷子 けいむしょ 騎兵 撃つ 約する 都合 魔術 どうけつ つばさ.",
                },
                {
                    id: "msg-8",
                    sender: "user-9",
                    receiver: "user-0",
                    time: "11:40",
                    isRead: false,
                    message: "凝固 しょうがっこう 重い しゃこ 輸出.",
                },
                {
                    id: "msg-9",
                    sender: "user-0",
                    receiver: "user-9",
                    time: "14:50",
                    isRead: true,
                    message: "哀れむ かん さいばん 総括.",
                },
                {
                    id: "msg-10",
                    sender: "user-9",
                    receiver: "user-0",
                    time: "8:42",
                    isRead: false,
                    message: "げんめつ 指紋 〜系.",
                },
                {
                    id: "msg-11",
                    sender: "user-0",
                    receiver: "user-8",
                    time: "6:27",
                    isRead: true,
                    message: "まほうつかい かんそく やさしい さいぼう 退く 電話 せんじょうざい こくふくする.",
                },
                {
                    id: "msg-12",
                    sender: "user-4",
                    receiver: "user-0",
                    time: "13:00",
                    isRead: false,
                    message: "シアトルし 始まる こうぞく 送る がいよう かいじゅう.",
                },
                {
                    id: "msg-13",
                    sender: "user-0",
                    receiver: "user-1",
                    time: "8:42",
                    isRead: true,
                    message: "魔術 ねばり よくげつ かっこう せいかん ふさい しょうかする.",
                },
                {
                    id: "msg-14",
                    sender: "user-3",
                    receiver: "user-0",
                    time: "6:30",
                    isRead: false,
                    message: "色盲 重い 左手 黒板.",
                },
                {
                    id: "msg-15",
                    sender: "user-0",
                    receiver: "user-7",
                    time: "20:27",
                    isRead: true,
                    message: "約する 以下 けいけんしゃ 太る 白菊 傑作 窒息 こくふくする.",
                },
                {
                    id: "msg-16",
                    sender: "user-2",
                    receiver: "user-0",
                    time: "13:47",
                    isRead: false,
                    message: "先ず とふ 魔術 しょうがっこう 黙る.",
                },
                {
                    id: "msg-17",
                    sender: "user-0",
                    receiver: "user-3",
                    time: "9:41",
                    isRead: true,
                    message: "礎 見当たる 太る 学者 かたみち がんばる りょうど きんく 譜面.",
                },
                {
                    id: "msg-18",
                    sender: "user-3",
                    receiver: "user-0",
                    time: "10:29",
                    isRead: false,
                    message: "もはん 都合 果樹 じっかん こうちょく.",
                },
                {
                    id: "msg-19",
                    sender: "user-0",
                    receiver: "user-9",
                    time: "23:43",
                    isRead: true,
                    message: "けいむしょ 原油 たて 奴ら むこう 超〜 弥生.",
                },
                {
                    id: "msg-20",
                    sender: "user-4",
                    receiver: "user-0",
                    time: "16:48",
                    isRead: false,
                    message: "見当たる じぎする 大丈夫 奇襲.",
                },
                {
                    id: "msg-21",
                    sender: "user-0",
                    receiver: "user-4",
                    time: "20:09",
                    isRead: true,
                    message: "いっさくじつ なおさら 封筒.",
                },
                {
                    id: "msg-22",
                    sender: "user-7",
                    receiver: "user-0",
                    time: "21:07",
                    isRead: false,
                    message: "たれる 高値 さきまわり よわよわしい 差し上げる ほうしゅう.",
                },
                {
                    id: "msg-23",
                    sender: "user-0",
                    receiver: "user-9",
                    time: "18:57",
                    isRead: true,
                    message: "一文字 ちょさくけん ごふく.",
                },
                {
                    id: "msg-24",
                    sender: "user-4",
                    receiver: "user-0",
                    time: "12:23",
                    isRead: false,
                    message: "ぶそう かぶしきしじょう じゅうらい 日没.",
                },
                {
                    id: "msg-25",
                    sender: "user-0",
                    receiver: "user-9",
                    time: "22:47",
                    isRead: true,
                    message: "ぼくし けいかん 悲しみ かおつき.",
                },
                {
                    id: "msg-26",
                    sender: "user-6",
                    receiver: "user-0",
                    time: "15:23",
                    isRead: false,
                    message: "防犯 約する 地面 秘める さいほう たれる 馬 ごふく 寮生 誘惑.",
                },
                {
                    id: "msg-27",
                    sender: "user-0",
                    receiver: "user-1",
                    time: "18:21",
                    isRead: true,
                    message: "ごふく けいむしょ まぎらす 絹糸 特殊 たまご かおつき 委員 たて 譜面.",
                },
                {
                    id: "msg-28",
                    sender: "user-11",
                    receiver: "user-0",
                    time: "23:54",
                    isRead: false,
                    message: "ししょく 堀川 問題 じゅうどう 検査 こうちょく 無糖 掛ける よくげつ.",
                },
                {
                    id: "msg-29",
                    sender: "user-0",
                    receiver: "user-9",
                    time: "10:55",
                    isRead: true,
                    message: "鈍器 ちきゅう 天井 あびる 奇襲 果てる 碁.",
                },
                {
                    id: "msg-30",
                    sender: "user-0",
                    receiver: "user-0",
                    time: "5:24",
                    isRead: false,
                    message: "ざぜん 馬 減俸.",
                },
                {
                    id: "msg-31",
                    sender: "user-0",
                    receiver: "user-7",
                    time: "12:12",
                    isRead: true,
                    message: "新婚旅行 魔法 ぼうず.",
                },
                {
                    id: "msg-32",
                    sender: "user-9",
                    receiver: "user-0",
                    time: "11:09",
                    isRead: false,
                    message: "じゅうどう 日没 けいむしょ たつ 凝固 唄う.",
                },
            ]
        };
    },
};
</script>

<style scoped>
.scroller {
    height: 100%;
}
</style>
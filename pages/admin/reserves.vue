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
                <v-list-item-group v-model="store_id" two-line>
                    <v-subheader>
                        店舗一覧
                    </v-subheader>
                    <v-list-item v-for="(item) in stores" :key="item.id" :value="item.id" @click="select()">
                        <v-list-item-content>
                            <v-list-item-title v-text="item.name"></v-list-item-title>
                            <v-list-item-subtitle v-text="item.description"></v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>
                </v-list-item-group>
            </v-list>
        </v-navigation-drawer>
        <v-navigation-drawer width="350" v-model="subdrawer" app right v-if="store_id"
            :style="$vuetify.breakpoint.mdAndDown ? 'width: 100%;' : ''">
            <v-container fluid style="height: 100%" v-if="resv_id">
                <v-list dense>
                    <v-subheader>
                        予約詳細
                    </v-subheader>
                    <v-list-item>
                        <v-list-item-subtitle >予約番号</v-list-item-subtitle>
                        <v-list-item-title>{{reserve.id}}</v-list-item-title>
                    </v-list-item>
                    <v-list-item>
                        <v-list-item-subtitle >店舗名</v-list-item-subtitle>
                        <v-list-item-title>{{reserve.store}}</v-list-item-title>
                    </v-list-item>
                    <v-subheader>
                        利用日時
                    </v-subheader>
                    <v-list-item>
                        <v-list-item-subtitle >開始</v-list-item-subtitle>
                        <v-list-item-title>2021/01/01</v-list-item-title>
                    </v-list-item>
                    <v-list-item>
                        <v-list-item-subtitle ></v-list-item-subtitle>
                        <v-list-item-title>12:00~</v-list-item-title>
                    </v-list-item>
                    <v-list-item>
                        <v-list-item-subtitle >終了</v-list-item-subtitle>
                        <v-list-item-title>2021/01/01</v-list-item-title>
                    </v-list-item>
                    <v-list-item>
                        <v-list-item-subtitle ></v-list-item-subtitle>
                        <v-list-item-title>~16:00</v-list-item-title>
                    </v-list-item>
                    <v-subheader>
                        支払い
                    </v-subheader>
                    <v-list-item>
                        <v-list-item-subtitle >支払方法</v-list-item-subtitle>
                        <v-list-item-title>クレジットカード</v-list-item-title>
                    </v-list-item>
                    <v-list-item>
                        <v-list-item-subtitle >支払日時</v-list-item-subtitle>
                        <v-list-item-title>2020/12/25 13:00</v-list-item-title>
                    </v-list-item>
                    <v-list-item>
                        <v-list-item-subtitle >ステータス</v-list-item-subtitle>
                        <v-list-item-title>✓支払い済</v-list-item-title>
                    </v-list-item>
                    <v-subheader>
                        顧客情報
                    </v-subheader>
                    <v-list-item>
                        <v-list-item-subtitle >顧客名</v-list-item-subtitle>
                        <v-list-item-title>{{reserve.user.name}}</v-list-item-title>
                    </v-list-item>
                    <v-list-item>
                        <v-list-item-subtitle >メール</v-list-item-subtitle>
                        <v-list-item-title>{{reserve.user.email}}</v-list-item-title>
                    </v-list-item>
                    <v-list-item>
                        <v-list-item-subtitle >電話番号</v-list-item-subtitle>
                        <v-list-item-title>{{reserve.user.phone}}</v-list-item-title>
                    </v-list-item>
                </v-list>
            </v-container>
            <v-container fluid style="height: 100%" class="d-flex align-center justify-center" v-else>
                <div class="text-center">
                    <v-icon class="text-h1">
                        mdi-hand-pointing-left
                    </v-icon>
                    <h3 class="text-h6">
                        予約を選択
                    </h3>
                    <p class="text-body-1">
                        リストから予約を選択して詳細を閲覧
                    </p>
                </div>
            </v-container>
        </v-navigation-drawer>
        <v-main>
            <v-container fluid style="height: 100%" v-if="store_id">
                <v-data-table
                    :headers="headers"
                    :items="reserves"
                    :items-per-page="10"
                    @click:row="resv_id = $event.id; subdrawer = true"
                ></v-data-table>
            </v-container>
            <v-container fluid style="height: 100%" class="d-flex align-center justify-center" v-else>
                <div class="text-center">
                    <v-icon class="text-h1">
                        mdi-hand-pointing-left
                    </v-icon>
                    <h3 class="text-h6">
                        店舗を選択
                    </h3>
                    <p class="text-body-1">
                        リストから店舗を選択して詳細を閲覧
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
            title: "予約管理",
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
            this.resv_id = ""
        },
    },
    computed: {
        reserves() {
            let res = this.reserves_items.filter(
                (reserve) => reserve.store_id === this.store_id
            );
            let rrr = [];
            res.forEach((reserve) => {
                rrr.push({
                    ...reserve,
                    store: this.apiStores.find(
                        (store1) => store1.id == reserve.store_id
                    ).name,
                    user: this.apiUsers.find((user1) => user1.id == reserve.user_id).name,
                })
            });
            return rrr;
        },
        reserve() {
            let res = this.reserves_items.find(resv => resv.id == this.resv_id)
            return {
                ...res,
                store: this.apiStores.find(
                    (store1) => store1.id == res.store_id
                ).name,
                user: this.apiUsers.find((user1) => user1.id == res.user_id),
            }
        },
        stores() {
            return this.apiStores;
        },
    },
    data() {
        return {
            drawer: true,
            subdrawer: true,
            store_id: "",
            resv_id: "",
            loading: true,
            headers: [
                { text: "予約番号", value: "id" },
                { text: "店舗", value: "store" },
                { text: "利用日時", value: "date" },
                { text: "顧客", value: "user" },
            ],
            reserves_items: [
                {
                    id: "RES-1",
                    store_id: "store-0",
                    date: "2021/01/01 12:00～14:00",
                    user_id: "user-1",
                },
                {
                    id: "RES-2",
                    store_id: "store-1",
                    date: "2021/01/01 12:00～14:00",
                    user_id: "user-3",
                },
                {
                    id: "RES-3",
                    store_id: "store-2",
                    date: "2021/01/01 12:00～14:00",
                    user_id: "user-1",
                },
                {
                    id: "RES-4",
                    store_id: "store-0",
                    date: "2021/01/02 17:00～19:00",
                    user_id: "user-5",
                },
                {
                    id: "RES-5",
                    store_id: "store-0",
                    date: "2021/01/03 18:30～20:30",
                    user_id: "user-6",
                },
                {
                    id: "RES-6",
                    store_id: "store-0",
                    date: "2021/01/04 19:30～21:30",
                    user_id: "user-2",
                },
                {
                    id: "RES-7",
                    store_id: "store-0",
                    date: "2021/01/05 18:00～20:00",
                    user_id: "user-4",
                },
            ],
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
            apiStores: [
                {
                    id: "store-0",
                    name: "スペース 大分県店",
                    description: "スペース 大分県店の説明文です。",
                    image: "https://loremflickr.com/640/480/animals",
                    capacity: 4,
                    prices: { base: 1908 },
                    google_calendar_id: "57d08ebb-e865-4833-88c1-f6d2e6bd9678@group.calendar.google.com",
                    payment: { credit: true, paypay: false },
                    preform: { credit: true, paypay: true },
                    messages: {
                        success:
                            "誤用 ぶっきょう ふくへい とくに 碁 ふん. 憂い 合う ねんじゅう おどろく. 泳ぐ じゅうどう あしくび せんりゅう この頃 じっかん 迷子.",
                        reminder:
                            "いちだい はちまき けしき かんりょうてき ほうしゅう ぼくし かぶしきしじょう. たいさ 果樹 無糖 浮世絵 とうさん むらさきいろ 始まる 無駄. 日没 りょうど ぼきん たて ちきゅう さくにゅう.",
                    },
                },
                {
                    id: "store-1",
                    name: "スペース 三重県店",
                    description: "スペース 三重県店の説明文です。",
                    image: "https://loremflickr.com/640/480/fashion",
                    capacity: 5,
                    prices: { base: 1432 },
                    google_calendar_id: "8f4033ff-04a7-4a5b-ab70-7d7f4ec4b66a@group.calendar.google.com",
                    payment: { credit: true, paypay: true },
                    preform: { credit: true, paypay: false },
                    messages: {
                        success: "つく つまる 下着 店舗 きひん 軒. こうせい 希望する はんだんする 問題. 黒板 老齢 はちまき ぐん. しばふ 憂い 切迫.",
                        reminder:
                            "約する こうつう 秘める はんだんする じじょでん さきまわり せっぷく ぶき 奴ら. 廃墟 ゆるむ しずむ. つぎつぎ はちまき たくす 絹糸 じょうだん. 撃つ 華やか みなもと とめる さいばん すいせん 頂く.",
                    },
                },
                {
                    id: "store-2",
                    name: "スペース 鹿児島県店",
                    description: "スペース 鹿児島県店の説明文です。",
                    image: "https://loremflickr.com/640/480/food",
                    capacity: 3,
                    prices: { base: 1269 },
                    google_calendar_id: "fe0dd0a5-30f7-4f53-a189-0b326d94cd70@group.calendar.google.com",
                    payment: { credit: true, paypay: false },
                    preform: { credit: true, paypay: false },
                    messages: {
                        success:
                            "仕事 交錯 さくにゅう 機嫌 十台 浅い 縛る 米国. いう 堀川 しゅしょう 太る 疾走 遮断 開閉 シアトルし しょうりゃく 遮断. 迷子 右利き 金 戦没 旧姓 みなもと 好き ちえん ぶき 評価. 備える 弥生 浮世絵 さくにゅう けしき 不可欠 せんたくする. むぜい あう 妥協する ちらかす 体重 とふ 軒 じゅうどう とうき. 碁 じょうき ぶそう 報じる がんばる じゅうどう とちょう 羊毛 ちあん.",
                        reminder:
                            "はっぽう 鍋 店 こうせい 退く 窒息 きょうふ 騎兵 先ず 巡回. そだてる あっとうする 病床 かぶしきしじょう 米国 おととい 悲しみ 既に 終点. しあとるし 犠牲 果てる 配慮 自立 歯を磨く あつい 待合 右翼 はなじ. 歯を磨く つく 原油 液体 投資 しえんする うえる ほうしゅう まほうつかい.",
                    },
                },
                {
                    id: "store-3",
                    name: "スペース 香川県店",
                    description: "スペース 香川県店の説明文です。",
                    image: "https://loremflickr.com/640/480/business",
                    capacity: 4,
                    prices: { base: 1017 },
                    google_calendar_id: "d8b8976f-46dc-4b15-9966-3875da966c1a@group.calendar.google.com",
                    payment: { credit: true, paypay: false },
                    preform: { credit: true, paypay: true },
                    messages: {
                        success:
                            "ふん 犠牲 むぼう 学者 山葵 ごうけん 全日本 にんい しゅしょう 終点. はりい 出版 液体 核実験 誘惑. ふそく 迷路 急騰. ちがい ひきざん 乗せる 乗せる はなはだ 済ます ごうけん 宜しく 陳列室 失う.",
                        reminder:
                            "きょうき みなもと 色盲 狂う かぜ 上手 誓い たいやく. じっかん 助手 話 憂い 自立 面 浮世絵 けいけんしゃ むこう 超音波. フランス語 ずいぶん 投資 かつ しょうがっこう 超〜 あしくび 同僚 そだてる.",
                    },
                },
                {
                    id: "store-4",
                    name: "スペース 茨城県店",
                    description: "スペース 茨城県店の説明文です。",
                    image: "https://loremflickr.com/640/480/nature",
                    capacity: 7,
                    prices: { base: 1210 },
                    google_calendar_id: "1d783136-6bac-42d4-9bf6-fbbad15caf1e@group.calendar.google.com",
                    payment: { credit: true, paypay: false },
                    preform: { credit: true, paypay: true },
                    messages: {
                        success:
                            "同僚 はちのす そあく 始まる 提案する はなじ 近視 さきまわり きもち ふさい. 部首 閉じる ねばり 底 おかね いじん せっぷく 好き. 報じる 下着 号. こうつう こくみん よくげつ 好き いつ頃 こわす じゅうらい 果樹. 泳ぐ きいろ ぐん まぎらす つばさ 底 よぼう 重い.",
                        reminder:
                            "電話 いじん 右利き がいよう ぶん ちあん こわす たいりく 羊毛. 殺人者 こいぬ あしくび. きょうどう 戦没 きょうしつ いっさくじつ 警官 こうぞく. ひかくする ひきざん 白菊 迷子. 手作り 壊す ふさい いちだい. 傑作 金 こくふくする 消す 魔法.",
                    },
                },
                {
                    id: "store-5",
                    name: "スペース 埼玉県店",
                    description: "スペース 埼玉県店の説明文です。",
                    image: "https://loremflickr.com/640/480/animals",
                    capacity: 3,
                    prices: { base: 1826 },
                    google_calendar_id: "6e870c82-c4c2-4acf-aff5-a87f1eac9574@group.calendar.google.com",
                    payment: { credit: true, paypay: false },
                    preform: { credit: true, paypay: true },
                    messages: {
                        success: "当て字 双 いちだい めいし 人口 きもち 交錯. ないしょばなし 日没 号 合う. りゅうこうご ざぜん 擬装 そんざい.",
                        reminder:
                            "つまる 指紋 悲しみ ちきゅう 下着 くつじょく がんばる ちょさくけん ぼくし ちあん. きょうどう にる はじめて ふん まぎらす 人性 しきもう はだか 飽くまでも ぞくご. たて しきもう 学院 そんざい ぼうず せんたくする 暇 しあとるし 禍根 むこう. あう 巡回 かい. ねんじゅう 輸出 店舗 以下. 伝統 見当たる じきしょうそう 不健康 しょうじょう.",
                    },
                },
                {
                    id: "store-6",
                    name: "スペース 鳥取県店",
                    description: "スペース 鳥取県店の説明文です。",
                    image: "https://loremflickr.com/640/480/transport",
                    capacity: 10,
                    prices: { base: 1613 },
                    google_calendar_id: "de01391d-9a5e-4806-8dd6-839cff0771b9@group.calendar.google.com",
                    payment: { credit: true, paypay: true },
                    preform: { credit: true, paypay: true },
                    messages: {
                        success:
                            "ほにゅうびん ちらかす 〜亭 かいぞく 徳川. 金 不健康 味噌 こわす 不思議 なんべい きょうふ. さいほう きょうかい 勇気. 不思議 よくげつ 見当たる うんがいい 窒息. はんそう 体重 輸出 さいほう. たいりく 黙る かい やさしい さいばん よぼう.",
                        reminder:
                            "一文字 隆起 天井 譜面 先ず 抑制 いちだい せんりゅう. あくれい きょうしつ 伐採 迷路 頂く おかね よわよわしい. 出版 あつかい 双 泳ぐ 白菊.",
                    },
                },
                {
                    id: "store-7",
                    name: "スペース 奈良県店",
                    description: "スペース 奈良県店の説明文です。",
                    image: "https://loremflickr.com/640/480/animals",
                    capacity: 5,
                    prices: { base: 1346 },
                    google_calendar_id: "c60f5d4b-8cd7-492a-8cf1-68f11daf34d4@group.calendar.google.com",
                    payment: { credit: true, paypay: false },
                    preform: { credit: true, paypay: false },
                    messages: {
                        success:
                            "警官 一文字 備える けいかん 下さい. どうけつ ねんじゅう 施行 いつ頃 こうせい 芸者 かんそく ほ 老齢. たまご あう 推奨 誘惑 あくれい だいどころ 暴力. せいしん ざぜん しゃっか 切迫. かちゅう 軒 ひきざん 白菊. あらそう 送る うえる.",
                        reminder:
                            "いつ頃 右翼 人性 みさき 原油 お盆 当て字 まつり みなと. たて 山葵 こはん 委員 屈む 血液 明治 総括 胃 境. 見当たる 双 つく あしくび 合う. 絹糸 塾生 あびる ハチのす. ふかさ とりあえず 半額 仕方がない 鍋 待合 鈍器 しずむ.",
                    },
                },
                {
                    id: "store-8",
                    name: "スペース 島根県店",
                    description: "スペース 島根県店の説明文です。",
                    image: "https://loremflickr.com/640/480/animals",
                    capacity: 5,
                    prices: { base: 1582 },
                    google_calendar_id: "c84762b8-9069-4879-b3c1-5955f4a1e98b@group.calendar.google.com",
                    payment: { credit: true, paypay: true },
                    preform: { credit: true, paypay: false },
                    messages: {
                        success:
                            "靖国神社 げいひんかん ぶそう ひがい きゅうりょう 悲しみ. お盆 入江 つばさ きょうしつ 浸す 急騰 特殊. きょだい たつ 弱虫. 貫く たれる けいじばん あおい 白菊 華やか 平安.",
                        reminder:
                            "ひがい 評価 旧姓 靖国神社 碁 きもち 日没 ゆるむ. 殺人者 双 ふん 芸者 馬鹿馬鹿しい 先ず たくす. とちょう 指紋 先週 唄う ちかく ふさい. 希望する かたみち あつい 合う きいろ むこう どうけつ どろ 送る たらす. 自宅 誓い かっこう おりめ たて 巡回 丼 当て字 しょくん.",
                    },
                },
                {
                    id: "store-9",
                    name: "スペース 石川県店",
                    description: "スペース 石川県店の説明文です。",
                    image: "https://loremflickr.com/640/480/technics",
                    capacity: 6,
                    prices: { base: 1724 },
                    google_calendar_id: "2646ed20-5b99-49da-986b-3eb95c96de32@group.calendar.google.com",
                    payment: { credit: true, paypay: true },
                    preform: { credit: true, paypay: true },
                    messages: {
                        success:
                            "フランス語 重い 閉じる しょうじょう フランス人 済ます 入江. ししょく こうぞく 床 きょうどう 気持ちいい たいりく はなはだ やさしい えきびょう. かぶしきしじょう はじめて 没落 ざぜん ひかくする いつ頃 あくれい よぼう. 合う きげんご あつい 汚す もはん かん 丼 旧姓 妥協する ずいぶん. 斬殺 きょうき どうけつ ほうせき 手作り 禍根 いちだい.",
                        reminder:
                            "丼 悲しみ 高値 切迫 ひかくする 問題. ごうけん 掛ける 先週 がいよう 独裁 はんだんする 米兵. ごふく くつじょく 浸す. 約する みさき 誘惑 せいしん 擬装 やしなう おんとう 入江 抑制 双. ぶっきょう ぞくご あらす 禍根 頂く 漠然.",
                    },
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
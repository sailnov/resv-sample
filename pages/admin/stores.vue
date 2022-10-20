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
                <v-dialog v-model="dialog1" width="500">
                    <template v-slot:activator="{ on, attrs }">
                        <v-btn color="pink" fab dark absolute bottom right class="mb-14" v-bind="attrs" v-on="on">
                            <v-icon>mdi-plus</v-icon>
                        </v-btn>
                    </template>
                    <v-card>
                        <v-card-title>
                            <span>新規店舗の追加</span>
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
                            v-model="dialog1_form.image"
                            label="店舗画像"
                            required></v-text-field>
                            <v-text-field
                            v-model="dialog1_form.capacity"
                            label="キャパシティ"
                            required></v-text-field>
                            <v-text-field
                            v-model="dialog1_form.prices.base"
                            label="基本料金"
                            required></v-text-field>
                            <v-text-field
                            v-model="dialog1_form.google_calendar_id"
                            label="グーグルカレンダーID"
                            required></v-text-field>
                            <v-switch v-model="dialog1_form.preform.credit" color="primary" label="クレジットカード" class="ps-2">
                            </v-switch>
                            <v-switch v-model="dialog1_form.preform.paypay" color="primary" label="PayPay" inlist="true"
                                class="ps-2"></v-switch>
                            <v-switch v-model="dialog1_form.payment.credit" color="primary" label="クレジットカード" class="ps-2">
                            </v-switch>
                            <v-switch v-model="dialog1_form.payment.paypay" color="primary" label="PayPay" inlist="true"
                                class="ps-2"></v-switch>
                            <v-textarea
                            v-model="dialog1_form.messages.success"
                            outlined
                            label="予約完了メッセージ"
                            ></v-textarea>
                            <v-textarea
                            v-model="dialog1_form.messages.reminder"
                            outlined
                            label="リマインドメッセージ"
                            ></v-textarea>
                        </v-card-text>
                        <v-card-actions>
                            <v-btn color="primary" text @click="addStores">
                                追加
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </v-dialog>
            </v-list>
        </v-navigation-drawer>
        <v-navigation-drawer width="350" v-model="subdrawer" app right v-if="store_id"
            :style="$vuetify.breakpoint.mdAndDown ? 'width: 100%;' : ''">
            <v-card flat>
                <v-img :src="store.image" height="200px" v-if="store.image" flat>
                    <template v-slot:placeholder>
                        <v-row class="fill-height ma-0" align="center" justify="center">
                            <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
                        </v-row>
                    </template>
                </v-img>
                <v-card-title v-text="store.name"></v-card-title>
                <v-card-subtitle v-text="store.description"></v-card-subtitle>
                <v-list-item dense>
                    <v-list-item-icon>
                        <v-icon>
                            mdi-currency-jpy
                        </v-icon>
                    </v-list-item-icon>
                    <v-list-item-content>
                        <v-list-item-title class="text-subtitle-2 text--secondary">{{store.prices.base}}円
                        </v-list-item-title>
                    </v-list-item-content>
                </v-list-item>
                <v-list-item dense>
                    <v-list-item-icon>
                        <v-icon>
                            mdi-account-group
                        </v-icon>
                    </v-list-item-icon>
                    <v-list-item-content>
                        <v-list-item-title class="text-subtitle-2 text--secondary">～{{store.capacity}}人まで
                        </v-list-item-title>
                    </v-list-item-content>
                </v-list-item>
            </v-card>
        </v-navigation-drawer>
        <v-main>
            <v-container fluid style="height: 100%" v-if="store_id">
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
                            :return-value.sync="store.name"
                            @save="store.name = $event"
                            >
                            <v-list-item-title>{{store.name}}</v-list-item-title>
                            <v-list-item-subtitle>店舗名</v-list-item-subtitle>
                            <template v-slot:input>
                                <v-text-field
                                v-model="store.name"
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
                                mdi-text
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-edit-dialog
                            :return-value.sync="store.description"
                            @save="store.description = $event"
                            >
                            <v-list-item-title>{{store.description}}</v-list-item-title>
                            <v-list-item-subtitle>説明文</v-list-item-subtitle>
                            <template v-slot:input>
                                <v-text-field
                                v-model="store.description"
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
                                mdi-image
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-edit-dialog
                            :return-value.sync="store.image"
                            @save="store.image = $event"
                            >
                            <v-list-item-title>{{store.image}}</v-list-item-title>
                            <v-list-item-subtitle>店舗画像</v-list-item-subtitle>
                            <template v-slot:input>
                                <v-text-field
                                v-model="store.image"
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
                                mdi-account
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-edit-dialog
                            :return-value.sync="store.capacity"
                            @save="store.capacity = $event"
                            >
                            <v-list-item-title>{{store.capacity}}人</v-list-item-title>
                            <v-list-item-subtitle>キャパシティ</v-list-item-subtitle>
                            <template v-slot:input>
                                <v-text-field
                                v-model="store.capacity"
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
                                mdi-currency-jpy
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-edit-dialog
                            :return-value.sync="store.prices.base"
                            @save="store.prices.base = $event"
                            >
                            <v-list-item-title>{{store.prices.base}}円</v-list-item-title>
                            <v-list-item-subtitle>ベース料金</v-list-item-subtitle>
                            <template v-slot:input>
                                <v-text-field
                                v-model="store.prices.base"
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
                                mdi-calendar
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-edit-dialog
                            :return-value.sync="store.google_calendar_id"
                            @save="store.google_calendar_id = $event"
                            >
                            <v-list-item-title>{{store.google_calendar_id}}</v-list-item-title>
                            <v-list-item-subtitle>Googleカレンダー ID</v-list-item-subtitle>
                            <template v-slot:input>
                                <v-text-field
                                v-model="store.google_calendar_id"
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
                                mdi-credit-card-settings-outline
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-list-item-title v-if="store.payment" class="d-flex">
                                <v-switch v-model="store.payment.credit" color="primary" label="クレジットカード" class="ps-2">
                                </v-switch>
                                <v-switch v-model="store.payment.paypay" color="primary" label="PayPay" inlist="true"
                                    class="ps-2"></v-switch>
                            </v-list-item-title>
                            <v-list-item-subtitle>決済方法</v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>
                    <v-divider inset></v-divider>
                    <v-list-item>
                        <v-list-item-icon>
                            <v-icon color="indigo">
                                mdi-credit-card-settings-outline
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-list-item-title v-if="store.preform" class="d-flex">
                                <v-switch v-model="store.preform.credit" color="primary" label="クレジットカード" class="ps-2">
                                </v-switch>
                                <v-switch v-model="store.preform.paypay" color="primary" label="PayPay" inlist="true"
                                    class="ps-2"></v-switch>
                            </v-list-item-title>
                            <v-list-item-subtitle>予約時に収集</v-list-item-subtitle>
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
                            <v-edit-dialog
                            :return-value.sync="store.messages.success"
                            @save="store.messages.success = $event"
                            >
                            <v-list-item-title>
                                {{store.messages.success}}
                            </v-list-item-title>
                            <v-list-item-subtitle>予約成功時メッセージ</v-list-item-subtitle>
                            <template v-slot:input>
                                <v-textarea
                                v-model="store.messages.success"
                                label="編集"
                                counter
                                ></v-textarea>
                            </template>
                            </v-edit-dialog>
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
                            <v-edit-dialog
                            :return-value.sync="store.messages.reminder"
                            @save="store.messages.reminder = $event"
                            >
                            <v-list-item-title>
                                {{store.messages.reminder}}
                            </v-list-item-title>
                            <v-list-item-subtitle>リマインドメッセージ</v-list-item-subtitle>
                            <template v-slot:input>
                                <v-textarea
                                v-model="store.messages.reminder"
                                label="編集"
                                counter
                                ></v-textarea>
                            </template>
                            </v-edit-dialog>
                        </v-list-item-content>
                    </v-list-item>
                    <v-divider inset></v-divider>
                    <v-list-item>
                        <v-list-item-icon>
                            <v-icon color="indigo">
                                mdi-trash-can
                            </v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-list-item-title>
                                <v-btn small color="error" @click="deleteStore">
                                    この店舗を削除する
                                </v-btn>
                            </v-list-item-title>
                            <v-list-item-subtitle>
                                削除
                            </v-list-item-subtitle>
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
            title: "店舗管理",
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
        deleteStore() {
            this.sid = this.store_id
            this.store_id = "";
            this.apiStores = this.apiStores.filter((store) => store.id !== this.sid)
        },
        addStores() {
            this.apiStores.push({
                ...this.dialog1_form,
                id: `store-${this.apiStores.length + 1}`,
            })
            this.dialog1 = false
            this.dialog1_form = {
                id: "",
                name: "",
                description: "",
                image: "",
                capacity: 0,
                prices: { base: 0 },
                google_calendar_id: "",
                payment: { credit: true, paypay: true },
                preform: { credit: true, paypay: true },
                messages: {
                    success:"",
                    reminder:"",
                },
            }
        }
    },
    data() {
        return {
            drawer: true,
            subdrawer: true,
            store_id: "",
            loading: true,
            dialog1: false,
            dialog1_form: {
                id: "",
                name: "",
                description: "",
                image: "",
                capacity: 0,
                prices: { base: 0 },
                google_calendar_id: "",
                payment: { credit: true, paypay: true },
                preform: { credit: true, paypay: true },
                messages: {
                    success:"",
                    reminder:"",
                },
            },
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
    computed: {
        stores() {
            return this.apiStores;
        },
        store() {
            return this.apiStores.find((store) => store.id == this.store_id);
        },
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
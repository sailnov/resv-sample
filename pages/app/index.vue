<template>
    <v-app>
        <v-app-bar app color="white" flat dense>
            <v-tabs centered class="ml-n9" color="grey darken-1" v-model="tab">
                <v-tab v-for="(item, index) in stores" :key="index">
                    {{ item.region }}
                </v-tab>
            </v-tabs>
        </v-app-bar>
        <v-main class="grey lighten-3">
            <v-container class="my-4">
                <v-tabs-items v-model="tab">
                    <v-tab-item v-for="(item, index) in stores" :key="index">
                        <v-row class="grey lighten-3">
                            <v-col cols="12" md="6" lg="4" xl="3" v-for="(store, index) in item.items"
                                :key="index + 'stre'">
                                <v-card flat>
                                    <div class="d-flex flex-no-wrap justify-space-between align-center">
                                        <v-img :src="store.image" class="ma-3" width="30%"></v-img>
                                        <div>
                                            <v-card-title v-text="store.name"></v-card-title>
                                            <v-card-subtitle v-text="store.description"></v-card-subtitle>
                                            <v-card-text class="my-0 py-0">
                                                <div class="text-subtitle-2">
                                                    {{store.access}}
                                                </div>
                                            </v-card-text>
                                            <v-card-actions>
                                                <v-btn block depressed @click="nowselect = store; dialog=true">予約する
                                                </v-btn>
                                            </v-card-actions>
                                        </div>
                                    </div>
                                </v-card>
                            </v-col>
                        </v-row>
                    </v-tab-item>
                </v-tabs-items>
            </v-container>
        </v-main>
        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="text-h5">{{nowselect.name}} を予約する</span>
                </v-card-title>
                <v-card-text>
                    <div class="text-center">
                        <v-menu v-model="menu2" :close-on-content-click="false" :nudge-right="40"
                            transition="scale-transition" offset-y min-width="auto">
                            <template v-slot:activator="{ on, attrs }">
                                <v-text-field v-model="selectdate" label="予約日を選択してください" prepend-icon="mdi-calendar"
                                    readonly filled v-bind="attrs" v-on="on"></v-text-field>
                            </template>
                            <v-date-picker v-model="selectdate" no-title @input="menu2 = false" show-current
                                locale="ja-jp" :min="new Date().toISOString()"></v-date-picker>
                        </v-menu>
                    </div>
                    <!-- time table -->
                    <div class="text-center" v-if="selectdate">
                        <v-select :items="times" v-model="selecttime" filled prepend-icon="mdi-clock-time-three-outline"
                            label="時刻を選択してください"></v-select>
                    </div>
                    <div v-if="(selectdate && selecttime)" class="d-md-flex justify-md-space-between">
                        <v-list dense>
                            <v-list-item>
                                <v-list-item-content>
                                <v-list-item-title>レンタルスペース{{nowselect.name}}</v-list-item-title>
                                <v-list-item-subtitle>店舗名</v-list-item-subtitle>
                                </v-list-item-content>
                            </v-list-item>
                            <v-list-item>
                                <v-list-item-content>
                                <v-list-item-title>{{nowselect.address}}</v-list-item-title>
                                <v-list-item-subtitle>店舗所在地</v-list-item-subtitle>
                                </v-list-item-content>
                            </v-list-item>
                            <v-list-item>
                                <v-list-item-content>
                                <v-list-item-title>{{nowselect.tel}}</v-list-item-title>
                                <v-list-item-subtitle>電話番号</v-list-item-subtitle>
                                </v-list-item-content>
                            </v-list-item>
                            <v-list-item>
                                <v-list-item-content>
                                <v-list-item-title>￥{{nowselect.price}} / 時間</v-list-item-title>
                                <v-list-item-subtitle>料金</v-list-item-subtitle>
                                </v-list-item-content>
                            </v-list-item>
                        </v-list>
                        <v-list dense>
                            <v-list-item>
                                <v-list-item-content>
                                <v-list-item-title>田中太郎</v-list-item-title>
                                <v-list-item-subtitle>お客様のお名前</v-list-item-subtitle>
                                </v-list-item-content>
                            </v-list-item>
                            <v-list-item>
                                <v-list-item-content>
                                <v-list-item-title>080-0101-0101</v-list-item-title>
                                <v-list-item-subtitle>電話番号</v-list-item-subtitle>
                                </v-list-item-content>
                            </v-list-item>
                            <v-list-item>
                                <v-list-item-content>
                                <v-list-item-title>クレジットカード</v-list-item-title>
                                <v-list-item-subtitle>お支払い方法</v-list-item-subtitle>
                                </v-list-item-content>
                            </v-list-item>
                        </v-list>
                    </div>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="nowselect = {};selectdate = ''; selecttime = '';dialog=false">
                        キャンセル
                    </v-btn>
                    <v-btn color="blue darken-1 white--text" depressed :disabled="(!selectdate && !selecttime)" @click="dialog=false;dialog2=true">
                        予約する
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-dialog v-model="dialog2" persistent max-width="600px">
            <v-card>
                <v-card-title class="justify-center">
                    <div class="text-center">
                        <v-icon class="text-h2 success--text">
                            mdi-check-circle-outline
                        </v-icon>
                        <h3 class="text-h5">予約が完了しました！</h3>
                    </div>
                </v-card-title>
                <v-card-text>
                    ご予約を承りました。
                    <br>
                    ご予約内容は、LINEでお知らせ致します。
                    <br>
                    また、マイページから確認できます。
                    <br>
                    ※この文章みたいなのは編集できるようにします
                </v-card-text>
                <v-card-actions>
                    <v-btn color="blue darken-1" text to="/" block>
                        ホームへ
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-app>
</template>

<script>
export default {
    data: () => ({
        nowselect: {},
        menu2: false,
        selectdate: "",
        selecttime: "",
        dialog: false,
        dialog2: false,
        times: [
            { "text": "06:00", "value": "06:00", "disabled": true },
            { "text": "07:00", "value": "07:00", "disabled": false },
            { "text": "08:00", "value": "08:00", "disabled": true },
            { "text": "09:00", "value": "09:00", "disabled": true },
            { "text": "11:00", "value": "10:00", "disabled": false },
            { "text": "12:00", "value": "12:00", "disabled": false },
            { "text": "13:00", "value": "13:00", "disabled": false },
            { "text": "14:00", "value": "14:00", "disabled": true },
            { "text": "15:00", "value": "15:00", "disabled": true },
            { "text": "16:00", "value": "16:00", "disabled": true },
            { "text": "17:00", "value": "17:00", "disabled": true },
            { "text": "18:00", "value": "18:00", "disabled": true },
            { "text": "19:00", "value": "19:00", "disabled": false },
            { "text": "20:00", "value": "20:00", "disabled": false },
            { "text": "21:00", "value": "21:00", "disabled": false },
            { "text": "22:00", "value": "22:00", "disabled": false },
            { "text": "23:00", "value": "23:00", "disabled": false },
        ],
        stores: [
            {
                "region": "東京",
                "items": [
                    {
                        "name": "渋谷店",
                        "address": "東京都渋谷区渋谷1-1-1",
                        "tel": "03-1234-5678",
                        "access": "渋谷駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                    {
                        "name": "新宿店",
                        "address": "東京都新宿区新宿1-1-1",
                        "tel": "03-1234-5678",
                        "access": "新宿駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                    {
                        "name": "池袋店",
                        "address": "東京都豊島区池袋1-1-1",
                        "tel": "03-1234-5678",
                        "access": "池袋駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                    {
                        "name": "品川店",
                        "address": "東京都品川区品川1-1-1",
                        "tel": "03-1234-5678",
                        "access": "品川駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                    {
                        "name": "横浜店",
                        "address": "神奈川県横浜市中区横浜1-1-1",
                        "tel": "03-1234-5678",
                        "access": "横浜駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                ]
            },
            {
                "region": "大阪",
                "items": [
                    {
                        "name": "大阪店",
                        "address": "大阪府大阪市中央区大阪1-1-1",
                        "tel": "06-1234-5678",
                        "access": "大阪駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                    {
                        "name": "堺店",
                        "address": "大阪府堺市中央区堺1-1-1",
                        "tel": "06-1234-5678",
                        "access": "堺駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                    {
                        "name": "神戸店",
                        "address": "兵庫県神戸市中央区神戸1-1-1",
                        "tel": "06-1234-5678",
                        "access": "神戸駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                    {
                        "name": "京都店",
                        "address": "京都府京都市中央区京都1-1-1",
                        "tel": "06-1234-5678",
                        "access": "京都駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                ]
            },
            {
                "region": "福岡",
                "items": [
                    {
                        "name": "福岡店",
                        "address": "福岡県福岡市中央区福岡1-1-1",
                        "tel": "092-1234-5678",
                        "access": "福岡駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                    {
                        "name": "北九州店",
                        "address": "福岡県北九州市中央区北九州1-1-1",
                        "tel": "092-1234-5678",
                        "access": "北九州駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                    {
                        "name": "筑紫店",
                        "address": "福岡県筑紫野市中央区筑紫1-1-1",
                        "tel": "092-1234-5678",
                        "access": "筑紫駅から徒歩5分",
                        "image": "https://placehold.jp/1280x720.png",
                        "description": "街の中心にあるです。",
                        "price": "1000",
                    },
                ]
            }
        ],
        tab: null,
    }),
}
</script>
<template>
    <v-app>
        <v-dialog persistent no-click-animation hide-overlay max-width="500" value="true">
            <v-card>
                <v-card-title class="text-h5 justify-center">
                    予約管理システム サンプル
                </v-card-title>
                <v-card-subtitle class="text-center pt-1">
                    v1.0.0 | &copy; 2022 Koki Matsuo.
                </v-card-subtitle>
                <v-card-text class="text-center">
                    予約管理システムのサンプルです。<br>
                    店舗画面、顧客画面のUIが確認できます。<br> <br> 
                    このサイトは、UIのイメージを確認するために作成したものです。<br> 
                    すべての機能は実際に動作せず、データは保存されません。<br> 
                    また、一部の細かい機能（例えば、細かい価格設定等）は実装されていません。<br> <br> 

                    デザインは変更される可能性があります。<br> <br> 

                    なにかお気づきの点（実装して欲しい機能、表示の変更等）がありましたら、<br> 
                    お気軽にご連絡ください。<br> 
                </v-card-text>
                <v-card-actions class="justify-space-between">
                    <v-btn to="/admin/" color="primary" x-large depressed>
                        店舗画面
                    </v-btn>
                    <v-btn to="/app/" color="primary" x-large depressed>
                        顧客画面
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-app>
</template>

<script>
export default {
    name: "IndexPage",
    layout: "empty",
    head() {
        return {
            title: "トップページ",
        };
    },
    data() {
        return {
        };
    },
};
</script>

<style scoped>

</style>
<template>
    <div class="cardRow">
        <div class="imageLogo">
            <img src="@/assets/x-logo.png">
        </div>

        <div class="registerArea">
            <div class="headTitle">Şu anda olup</div>
            <div class="headTitle">bitenler</div>
            <div class="headDesc">Hemen katıl.</div>

            <TWButton label="Hesap oluştur" class="toRouter" @click="toHome('register')" />
            <div class="termStyle">Üye olarak gizlilik politiklarını kabul etmiş olursunuz.</div>

            <div class="headDesc">Zaten bir hesabım var</div>
            <TWButton label="Giriş yap" class="toRouter" @click="toHome('login')" />
        </div>
    </div>
</template>

<script>
import { useRouter } from 'vue-router';
export default {
    name: 'WelcomeView',
    setup() {
        const router = useRouter();
        function toHome(event) {
            router.push(`${event}`)
        }
        return { toHome }
    }
}
</script>

<style scoped>
@media only screen and (max-width: 600px) {
    img {
        width: 100px;
        height: 100px;
    }
}


.imageLogo {
    width: 30%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
}



.registerArea {
    width: 70%;
    height: 100%;
    display: flex;
    justify-content: center;
    flex-direction: column;
}

.headTitle {
    font-weight: bold;
    font-size: 70px;
}

.headDesc {
    font-weight: bold;
    font-size: 30px;
    margin-top: 1.25rem;
}

.toRouter {
    border-radius: 14px;
    border: 1px solid #25abe1ff;
    background-color: #25abe1ff;
    color: #fff;
    width: 14.25rem;
    height: 2.2rem;
    margin-top: 30px;
    display: flex;
}

.toRouter:hover {
    background-color: rgb(20, 133, 209);
}

.termStyle {
    font-size: 12px;
    margin-top: 10px;
}

.cardRow {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
    position: absolute;

}
</style>
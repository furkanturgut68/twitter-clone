<template>
  <div class="feedArea">
    <TWToast></TWToast>
    <div>
      <div class="topBanner"><span style="font-weight: bold; margin-left: 10px;">Anaysayfa</span> <i class="pi pi-star"
          style="float: right; margin-right: 20px; margin-top: 5px;"></i>
      </div>

      <div class="enterTweet">
        <TWCircleImage class="tweetUserPhoto" :image="profilePhoto" size="large" shape="circle" />
        <TWTextarea class="tweetArea" maxRows="2" v-model="enteredTweet" autoResize maxlength="250" placeholder="Naber ?">
        </TWTextarea>
        <small style="display: flex; justify-content: center; font-size: 12px; color: #25abe1ef;">{{ tweetLenght
        }}</small>
        <div>
          <TWButton style="background-color: #25abe1ef; margin: 20px;" icon="pi pi-image"></TWButton>
          <TWButton @click="addTweet" class="buttonTweet" label="Tweet">
          </TWButton>
        </div>
      </div>

      <tweet-component  :tweetList="tweetList" ></tweet-component>

     
    </div>
  </div>
</template>

<script>
import saveTweet from '@/firebase/saveTweet.js';
import { ref, computed, onMounted } from 'vue';
import {
  getFirestore,
  query,
  collection,
  orderBy,
  onSnapshot,
  serverTimestamp,
 
} from "firebase/firestore";
import { useToast } from 'primevue/usetoast';
import { getAuth, onAuthStateChanged } from 'firebase/auth';
import { app } from '@/firebase/config';
import TweetComponent from './TweetComponent.vue';
export default {
  components: { TweetComponent },
  name: "HomeComponent",
  setup() {
    const profilePhoto = ref(null)
    const enteredTweet = ref('');
    const displayName = ref(null);
    const firestore = getFirestore();
    const userId = ref(null);
    const toast = useToast();
    const auth = getAuth(app);
    let tweetList = ref([]);

 

    //Photo id ve ve username alındı
    onMounted(async ()  => {
      onAuthStateChanged(auth, (user) => {
        if (user != null) {
          userId.value = user.uid;
          user.providerData.forEach((profile) => {
            displayName.value = profile.displayName;
            profilePhoto.value = profile.photoURL;
          });
        }
      });
    
    });

    // Tweet'in karakter sınırlaması yapıldı.
    const tweetLenght = computed(() => {
      return enteredTweet.value.length <= 250 ? enteredTweet.value.length + "/250" : 'Maxiumum 250 karakterle sınırlıdır'
    });

    //Tweet Atma
    const addTweet = async () => {
      //TweetList adında firestore'a kaydedildi
      if (enteredTweet.value.trim() !== '') {
        const tweetId = Date.now();
        const tweetData = {
          displayName: displayName.value,
          profile: profilePhoto.value,
          tweet: enteredTweet.value,
          tweetDate: serverTimestamp(),
          userId: userId.value,
          likeState:false,
          totalLike:0,
          tweetId:tweetId,
          likeUser:[]
        };

        await saveTweet(tweetData);
        enteredTweet.value = '';
        toast.add({ severity: "info", life: 2000, detail: "Tweet Gönderildi", summary: "Tweet" });
      }
    }

    //Tweet Çekme
    const getTweetList = () => {
      tweetList.value = []
      const tweetQuery = query(collection(firestore, "tweetLists"), orderBy("tweetDate", "desc"));
      onSnapshot(tweetQuery, (querySnapshot) => {
        querySnapshot.forEach(() => {
          tweetList.value = []
        });
      });
      onSnapshot(tweetQuery, (querySnapshot) => {
        querySnapshot.forEach((doc) => {
          tweetList.value.push(doc.data());

        });
      });
    }
    getTweetList();



    return { profilePhoto, tweetLenght, enteredTweet, addTweet, tweetList }
  }

}
</script>

<style scoped>
textarea {
  overflow-y: scroll;
  max-height: 80px;
  margin-top: 10px;
  margin-left: 10px;
  border: 1px solid #ffff;
  width: 80%;
}

.buttonTweet {
  float: right;
  background-color: #25abe1ef;
  margin: 20px;
}


.tweetUserPhoto {
  margin-left: 10px;
  margin-top: 10px;
}

.enterTweet {
  width: 98%;
  height: 180px;
  margin-top: 1px;
  margin-right: 1%;
  margin-left: 1%;
  background-color: #fff;
}

.topBanner {
  margin-right: 1%;
  margin-left: 1%;
  width: 98%;
  height: 50px;
  background-color: #fff;
}

i {
  font-size: 20px;
}

.feedArea {
  background-color: #e9e9e9;
  width: 60%;
  height: max-content;
}
</style>
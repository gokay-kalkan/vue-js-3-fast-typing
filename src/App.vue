<template>
  <div class="container jumbotron">
    <h1 class="display-4">Hızlı Yazma Yarışması</h1>
    <p class="lead">Ne kadar hızlı klavye kullandığını test et</p>

    <hr class="my-4" />

    <div v-if="isFinish" class="alert alert-primary">
      <h1>Oyun Bitti</h1>
      <p class="display-4"></p>
      <span>DKS {{ djsks }}</span>
      <span>Doğruluk Yüzdesi {{ truePercent }}</span>
      <span>Doğru Kelime: {{ trueCount }}</span>
      <span>Yanlış Kelime: {{ falseCount }}</span>
    </div>

    <div v-else>
      <div class="card">
        <div class="card-body">
          <span
            v-for="(word, key) in words.filter((data, index) => index < 20)"
            :key="key"
            v-bind:class="key != 0 || writingWordControl"
            class="ml-2 words"
          >
            {{ word }}
          </span>
        </div>
      </div>

      <div class="card">
        <div class="card-body bg-secondary">
          <div class="input-group input-group-lg">
            <input type="text" class="form-control" v-model="writingword" />
            <div class="input-group-append">
              <button class="btn btn-light" disabled type="button">
                {{ timer }}
              </button>

              <button class="btn btn-light" type="button" v-on:click="getWords">
                Yenile
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import wordList from "@/assets/words.json";
export default {
  data() {
    return {
      words: [],
      writingword: "",
      isTrue: true,
      trueCount: 0,
      falseCount: 0,
      timer: 10,
      interval: false,
      isRunning: false,
      isFinish: false,
      wordList: wordList,
    };
  },
  watch: {
    writingword(val) {
      if (!val || val === " ") {
        this.writingword = "";
        return; //kelime girilmediyse veya space tusuna basıldıysa bile oyun başlamasın.
      }
      if (!this.isRunning) this.toggleTimer(); //tuşa bastım ısRunning false ise çalıştı daha sonra aşağıda toggleTimer çalışacak ısRun true olacak ve bir daha her kelimeye bastıgında saniye inmeyecek yani ilk kelimede bir kere inecek

      const word = this.words[0].slice(0, val.length); //kullanıcının girdiği kelimeyide girilen uzunluğa göre indekslere ayır
      let userWord = val.replace(" ", ""); //kullanıcının girdiği veride boşluk varsa onu kes boşluksuz hali ile sadece girilen değerleri kontrol et
      this.isTrue = word === userWord; //kullanıcının girdiği veri ile kelimenin tüm indeksleri doğru ise true

      if (val.indexOf(" ") !== -1) {
        this.isTrue ? this.trueCount++ : this.falseCount++; //kullanıcı yazdığı kelime sırasında space tusuna bastıysa 2.kelimeye geçtiyse doğrumu yanlış mı bunu al
        this.words.splice(0, 1); //kullanıcı kelimeyi yazıp space yapınca kelimenin sıfırıncı indexi ile birinci indexi yer değiştir yeni sıfırıncı indexi geç diğer kelimeyi al
        this.writingword = ""; //kullanıcının girdiği kelimeyi spaceye basıp geçerse o kelimeyide inputtan temizle
        this.isTrue = true; //2.kelimeye geçtiyse ilk kelime yanlış olduktan sonra onu kırmızı yaptıktan sonra diğer kelimeyi kırmızı yapma griden
      }
    },
  },

  mounted() {
    this.getWords();
  },
  computed: {
    writingWordControl() {
      return this.isTrue ? "writing-word" : "writing-word bg-danger";
    },
    djsks() {
      return 300 - this.words.length;
    },
    truePercent() {
      const percent = 100 / this.djsks;
      return percent * this.trueCount;
    },
  },
  methods: {
    getWords() {
      this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0, 300); //300 kelime getir
    },

    toggleTimer() {
      this.isRunning = true; //true oldugu için bir daha saniye inmeyecek hızlı bir şekilde her kelime basıldıgında yoksa saniye hızlıca iner
      this.interval = setInterval(this.timeProcess, 1000); //1000 milisaniyede bir azalt süreyi interval ile dinle
    },
    timeProcess() {
      if (this.timer === 0) {
        clearInterval(this.interval); //saniye 0 olursa timerı daha milisaniye cinsinden aşağı indirme yoksa eksilere iner
        this.isFinish = true;
        return;
      }
      this.timer--; //saniyeyi bir bir azalt
    },
  },
};
</script>

<style>
.words {
  font-size: 25px;
}

.writing-word {
  background-color: slategrey;
  color: white;
  padding: 5px;
  border-radius: 5px;
}
</style>

<template>
  <div class="CAHO">
    <h3 class="CafeHome"><b-icon icon="house-door" scale="0.9"></b-icon> Cafe Home</h3>
    <div class="returnBtnDiv">
      <button class="returnBtn" @click="$router.go(-1)">
        <span><b-icon icon="arrow-left"></b-icon> 돌아가기</span>
      </button>
    </div>
    <div class="cafehome-card">
      <div class="cafepro">
        <b-avatar class="ABATAR" size="150px" button @click="$bvModal.show('modal-cafe-img')"
          ><b-icon v-if="!watchCafe.icon" icon="shop" scale="3.3"></b-icon>
          <img v-if="watchCafe.icon" class="ProIMG" :src="`${url}/uploads/${watchCafe.icon}`" />
        </b-avatar>
        <b-modal id="modal-cafe-img" title="카페 사진 추가" hide-footer>
          <b-form-group>
            <b-form-file
              v-model="avatar"
              :state="Boolean(avatar)"
              placeholder="사진 추가하기..."
              required
              accept="image/*"
              @change="previewImage"
            ></b-form-file>
            <div class="SUM">
              <b-img :src="previewImageData" class="formStyle"></b-img>
              <b-icon v-if="previewImageData" class="DelImg" icon="x" @click="deleteIMG"></b-icon>
            </div>
            <b-btn variant="success" @click="updateLogo">저장</b-btn>
          </b-form-group>
        </b-modal>
      </div>
      <div class="cafehoMeM">
        <b-row class="my-1">
          <b-col sm="3">
            <label for="id">카페이름:</label>
          </b-col>
          <b-col sm="6">
            <p v-if="on">
              {{ watchCafe.cafeName }}
              <button v-if="on" class="PhoneB" variant="outline-primary" @click="on = !on">
                <b-icon icon="pencil" class="pencil"></b-icon>
              </button>
            </p>
            <b-input v-if="!on" v-model="cafeName"></b-input>
            <b-btn v-if="!on" variant="danger" @click="on = !on">취소</b-btn>
          </b-col>
        </b-row>
        <b-row class="my-1">
          <b-col sm="3">
            <label for="id">카페주소:</label>
          </b-col>
          <b-col sm="8">
            <p v-if="eyes">
              {{ watchCafe.location }}
              <button v-if="eyes" class="PhoneB" variant="outline-primary" @click="eyes = !eyes">
                <b-icon icon="pencil" class="pencil"></b-icon>
              </button>
            </p>
            <b-input v-if="!eyes" v-model="location" disabled></b-input>
            <b-button v-if="!eyes" @click="showApi"> 주소검색 </b-button>
            <b-btn v-if="!eyes" variant="danger" @click="eyes = !eyes">취소</b-btn>
          </b-col>
        </b-row>
        <b-row class="my-1">
          <b-col sm="3">
            <label for="id">사업자 번호:</label>
          </b-col>
          <b-col sm="6">
            <p v-if="watch">
              {{ watchCafe.businessNum }}
              <button v-if="watch" class="PhoneB" variant="outline-primary" @click="watch = !watch">
                <b-icon icon="pencil" class="pencil"></b-icon>
              </button>
            </p>
            <b-input v-if="!watch" v-model="businessNum"></b-input>
            <b-btn v-if="!watch" variant="danger" @click="watch = !watch">취소</b-btn>
          </b-col>
        </b-row>
        <b-row class="my-1">
          <b-col sm="3">
            <label for="id">멤버쉽<br />만료기간:</label>
          </b-col>
          <b-col sm="7">
            <span>{{ setRealFormat(watchCafe.expireDate) }}</span>
            <b-btn class="memberB" @click="$bvModal.show('modal-membership')">멤버쉽 결제</b-btn>
            <b-modal id="modal-membership" title="멤버쉽 결제" hide-footer>
              <b-form-group>
                <p>한달 무료 이벤트 기간입니다.</p>
              </b-form-group>
            </b-modal>
          </b-col>
        </b-row>
        <div class="cafehomeB">
          <b-btn class="cafeSAVE" variant="success" @click="updateCafe">저장</b-btn>
          <b-btn class="cafehomeTB" @click="$bvModal.show('modal-cafe')">테블릿 사진 추가</b-btn>
          <b-modal id="modal-cafe" title="카페 사진 추가" hide-footer>
            <b-form-group>
              <b-form-file
                v-model="tablet"
                :state="Boolean(tablet)"
                placeholder="사진 추가하기..."
                required
                accept="image/*"
                @change="previewImage"
              ></b-form-file>
              <div>
                <b-img :src="previewImageData" class="formStyle"></b-img>
                <b-icon v-if="previewImageData" class="DelImg" icon="x" @click="deleteIMG"></b-icon>
              </div>
              <b-btn variant="success" @click="updateTablet">저장</b-btn>
            </b-form-group>
          </b-modal>
          <b-btn @click="$router.push(`/main/${$route.params.id}/customer`)">고객관리</b-btn>
        </div>
      </div>
    </div>
    <div class="deletCafe" style="cursor: pointer" @click="$bvModal.show('modal-deletCafe')">삭제하기</div>
    <b-modal id="modal-deletCafe" title="카페삭제" style="text-align: center" hide-footer>
      <b-form-group style="text-align: center">
        <p>정말로 카페를 삭제하시겠습니까?</p>
        <p>가입한 멤버쉽 정보를 살펴보신 후 눌러주세요.</p>
        <b-btn variant="danger" @click="deleteCafe">삭제하기</b-btn>
      </b-form-group>
    </b-modal>
  </div>
</template>

<script>
import axios from 'axios'
import SetFormat from '../../assets/mixins/SetFormat.vue'
export default {
  mixins: [SetFormat],
  data() {
    return {
      avatar: [],
      tablet: [],
      show: true,
      on: true,
      watch: true,
      eyes: true,
      previewImageData: null,
      watchCafe: {},
      cafeName: '',
      location: '',
      businessNum: '',
      url: ''
    }
  },
  mounted() {
    this.getWatchData()
  },
  methods: {
    onSubmit(evt) {
      evt.preventDefault()
      alert(JSON.stringify(this.avatar))
    },
    onReset(evt) {
      evt.preventDefault()
      // Reset
      this.avatar = []
      this.show = false
      this.$nextTick(() => {
        this.show = true
      })
    },
    previewImage(event) {
      let input = event.target

      if (input.files && input.files[0]) {
        let reader = new FileReader()
        reader.onload = e => {
          this.previewImageData = e.target.result
        }
        reader.readAsDataURL(input.files[0])
      } else {
        this.previewImageData = null
      }
    },
    deleteIMG() {
      this.previewImageData = null
      this.tablet = null
      this.avatar = null
    },

    async getWatchData() {
      await axios
        .get(process.env.VUE_APP_URL + `/cafe/info-one/${this.$route.params.id}`, {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`
          }
        })
        .then(response => {
          this.url = process.env.VUE_APP_URL
          console.log('getWatchData - response : ', response.data.data)
          this.watchCafe = response.data.data
        })
        .catch(error => {
          console.log('getWatchData - error : ', error)
        })
    },
    showApi() {
      if (typeof Postcode == 'undefined') {
        new window.daum.Postcode({
          oncomplete: data => {
            // 팝업에서 검색결과 항목을 클릭했을때 실행할 코드를 작성하는 부분.

            // 도로명 주소의 노출 규칙에 따라 주소를 조합한다.
            // 내려오는 변수가 값이 없는 경우엔 공백('')값을 가지므로, 이를 참고하여 분기 한다.
            let fullRoadAddr = data.roadAddress // 도로명 주소 변수
            let extraRoadAddr = '' // 도로명 조합형 주소 변수

            // 법정동명이 있을 경우 추가한다. (법정리는 제외)
            // 법정동의 경우 마지막 문자가 "동/로/가"로 끝난다.
            if (data.bname !== '' && /[동|로|가]$/g.test(data.bname)) {
              extraRoadAddr += data.bname
            }
            // 건물명이 있고, 공동주택일 경우 추가한다.
            if (data.buildingName !== '' && data.apartment === 'Y') {
              extraRoadAddr += extraRoadAddr !== '' ? ', ' + data.buildingName : data.buildingName
            }
            // 도로명, 지번 조합형 주소가 있을 경우, 괄호까지 추가한 최종 문자열을 만든다.
            if (extraRoadAddr !== '') {
              extraRoadAddr = ' (' + extraRoadAddr + ')'
            }
            // 도로명, 지번 주소의 유무에 따라 해당 조합형 주소를 추가한다.
            if (fullRoadAddr !== '') {
              fullRoadAddr += extraRoadAddr
            }

            // 우편번호와 주소 정보를 해당 필드에 넣는다.
            this.zip = data.zonecode //5자리 새우편번호 사용
            this.location = fullRoadAddr
          }
        }).open()
      }
    },
    async updateLogo() {
      const photoFormData = new FormData()
      photoFormData.append('icon', this.avatar)
      console.log('updateLogo - photoUrl : ', this.avatar)

      await axios
        .put(process.env.VUE_APP_URL + `/cafe/update-cafe/${this.watchCafe.id}`, photoFormData, {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`
          }
        })
        .then(response => {
          console.log('updateLogo - response : ', response)
          alert('카페 프로필 사진이 추가 되었습니다.')
          this.$router.go()
        })
        .catch(error => {
          console.log('updateLogo - error : ', error)
        })
    },
    async updateTablet() {
      const photoForData = new FormData()
      photoForData.append('img', this.tablet)
      console.log('updateTablet - photoUrl : ', this.tablet)

      await axios
        .put(process.env.VUE_APP_URL + `/cafe/update-cafe/${this.watchCafe.id}`, photoForData, {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`
          }
        })
        .then(response => {
          console.log('updateTablet - response : ', response)
          alert('테블릿 사진이 추가 되었습니다.')
          this.$router.go()
        })
        .catch(error => {
          console.log('updateTablet - error : ', error)
        })
    },
    async updateCafe() {
      const axiosBody = {
        cafeName: this.cafeName,
        location: this.location,
        businessNum: this.businessNum
      }
      console.log('updateCafe - axiosBody', axiosBody)

      await axios
        .put(process.env.VUE_APP_URL + `/cafe/update-cafe/${this.watchCafe.id}`, axiosBody, {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`
          }
        })
        .then(response => {
          console.log('updateCafe - response : ', response)
          alert('카페정보가 수정 되었습니다.')
          this.$router.go()
        })
        .catch(error => {
          console.log('updateCafe - error : ', error)
        })
    },
    async deleteCafe() {
      // console.log(this.watchCafe, this.watchCafe.cafeName)
      //const url = process.env.VUE_APP_URL + `/profile/remove-cafe/${this.watchCafe.id}`
      await axios
        .delete(process.env.VUE_APP_URL + `/cafe/remove-cafe/${this.watchCafe.id}`, {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('token')}`
          }
        })
        .then(response => {
          console.log('deleteCafe - response : ', response)
          alert('카페가 삭제 되었습니다.')
          this.$router.push('/main')
        })
        .catch(error => {
          console.log('deleteCafe - error : ', error)
        })
    }
  }
}
</script>

<style>
/* .SUM {
  display: flex;
  top: 0%;
} */
.DelImg {
  /* margin-bottom: 15%; */
  width: 20px;
  height: 20px;
  border-radius: 10px;
  background: rgb(192, 192, 192);
  transition: 0.5s;
}
.DelImg:hover {
  background: rgb(143, 143, 143);
}
.ABATAR {
  margin-right: 30px;
}
.CAHO {
  width: 100%;
  height: 100vh;
}
.cafehomeTB {
  margin-right: 10px;
}
.formStyle {
  max-height: 50%;
  width: 50%;
  margin-top: 15px;
  margin-bottom: 15px;
}
.cafehome-card {
  width: 100%;
  padding-left: 18%;
  display: grid;
  grid-template-columns: 1fr 6fr;
}
.CafeHome {
  margin: 25px;
}
.memberB {
  margin-left: 3vw;
  width: 120px;
  background-color: #5a38d4;
  transition: 0.5s;
}
.memberB:hover {
  background-color: #432a9f;
}
.cafehoMeM {
  width: 75%;
  padding: 30px;
  border-radius: 15px;
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.1), 0 3px 6px rgba(0, 0, 0, 0.1);
}
.cafehomeB {
  margin-top: 25px;
}
.ProIMG {
  width: 100%;
  height: 100%;
}
.cafeSAVE {
  margin-right: 10px;
}
.deletCafe {
  text-align: center;
  padding-top: 15px;
  padding-left: 50%;
  color: red;
}
.returnBtn {
  border: none;
  background-color: #eee;
  border-radius: 5px;
  color: grey;
  width: 100px;
}
.returnBtn:active {
  filter: brightness(80%);
}
.returnBtnDiv {
  display: flex;
  justify-content: flex-end;
  padding-right: 210px;
  padding-bottom: 20px;
}
</style>

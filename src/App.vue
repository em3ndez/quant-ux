<template>
  <div id="app" class="MatcMainCntr">
    <router-view/>
    <div class="vommondMessage" ref="message">
    </div>
  </div>
</template>

<style>
</style>
<script>
import css from 'dojo/css'
import win from 'dojo/win'
import Services from 'services/Services'

export default {
  methods: {
    showSuccess:function(msg){
			if(this.$refs.message){
				css.add(this.$refs.message, "vommondMessageSuccess");
				css.remove(this.$refs.message, "vommondMessageError vommondMessageHint");
				this.$refs.message.innerHTML = msg;
				setTimeout( () => {
					this.hideMessage()
				},2000);
			}
		},

	showError:function(msg){
		if(this.message){
				css.add(this.$refs.message, "vommondMessageError");
				css.remove(this.$refs.message, "vommondMessageSuccess vommondMessageHint");
				this.$refs.message.innerHTML = msg;
				setTimeout( () => {
					this.hideMessage()
				},4000);
		}
	},

	showHint:function(msg){
		if(this.$refs.message){
			css.add(this.$refs.message, "vommondMessageHint");
			css.remove(this.$refs.message, "vommondMessageSuccess vommondMessageSuccess");
			this.$refs.message.innerHTML = msg;
			setTimeout( () => {
				this.hideMessage()
			},4000);
		}
	},

	hideMessage:function(){
		css.remove(this.$refs.message, "vommondMessageSuccess vommondMessageError vommondMessageHint");
	},

	handler4xx (url, res) {
		if (res.status === 401) {
			alert('Something is wrong. Please login again!')
			Services.getUserService().logout()
			this.$router.push('/')
			this.$root.$emit('MatcLogout', Services.getUserService().GUEST)
		}
		if (res.tokenTimedOut) {
			alert('Your session has expired. Please login again')
			Services.getUserService().logout()
			this.$router.push('/')
			this.$root.$emit('MatcLogout', Services.getUserService().GUEST)
		}
	},

		setCookie(c_name, value, exdays = 10) {
			var exdate = new Date();
			exdate.setDate(exdate.getDate() + exdays);
			var c_value = escape(value) + ((exdays == null) ? "" : "; expires=" + exdate.toUTCString());
			document.cookie = c_name + "=" + c_value;
		}
  },
  mounted () {
		this.setCookie('quant-ux-ui', '4.0.0.OSS')
		Services.getUserService().load()
		Services.setErrorHandler((url, res) => {
			this.handler4xx(url, res)
		})
    this.$root.$on('Success', (msg) => {
      this.showSuccess(msg)
    })
    this.$root.$on('Error', (msg) => {
      this.showError(msg)
    })
    this.$root.$on('Hint', (msg) => {
      this.showHint(msg)
		})
		this.$root.$on('UserLogin', (user) => {
			Services.getUserService().setUser(user)
		})
		css.remove(win.body(), 'MatcPublic')

		let language = Services.getUserService().getLanguage()
		this.$root.$i18n.locale = language
  }
}
</script>


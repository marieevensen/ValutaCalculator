<template>
	<main class="converter" v-if="!error">
        <h1 class="converter__title">VALUTACALCULATOR</h1>

        <p class="converter__date">
            What is the currency today? <br>
            {{ today }}
        </p>

        <div class="converter__euro">
            <label class="euro__text">eur</label>
                
            <input type="text" class="euro__valuta" v-model="eurValuta">

            <button class="euro__calculate" @click="calculate" aria-label="Click here to convert the valuta">
                <svg width="30" height="30" viewBox="0 0 300 300" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <path d="M300 150C300 189.782 284.196 227.936 256.066 256.066C227.936 284.196 189.782 300 150 300C110.218 300 72.0644 284.196 43.934 256.066C15.8035 227.936 0 189.782 0 150C0 110.218 15.8035 72.0644 43.934 43.934C72.0644 15.8035 110.218 0 150 0C189.782 0 227.936 15.8035 256.066 43.934C284.196 72.0644 300 110.218 300 150V150ZM159.375 84.375C159.375 81.8886 158.387 79.504 156.629 77.7459C154.871 75.9877 152.486 75 150 75C147.514 75 145.129 75.9877 143.371 77.7459C141.613 79.504 140.625 81.8886 140.625 84.375V192.994L100.388 152.737C98.6271 150.977 96.2395 149.988 93.75 149.988C91.2605 149.988 88.8729 150.977 87.1125 152.737C85.3521 154.498 84.3632 156.885 84.3632 159.375C84.3632 161.865 85.3521 164.252 87.1125 166.013L143.362 222.263C144.233 223.136 145.268 223.828 146.407 224.301C147.546 224.773 148.767 225.017 150 225.017C151.233 225.017 152.454 224.773 153.593 224.301C154.732 223.828 155.767 223.136 156.638 222.263L212.888 166.013C214.648 164.252 215.637 161.865 215.637 159.375C215.637 156.885 214.648 154.498 212.888 152.737C211.127 150.977 208.74 149.988 206.25 149.988C203.76 149.988 201.373 150.977 199.612 152.737L159.375 192.994V84.375Z" fill="#982FA9"/>
                </svg>
            </button>
        </div>

        <div class="converter__country-code">
            <select class="country-code__text" v-model="selectedValuta" aria-label="Type or use up and down to change the valuta">
                <option v-for="valuta, index in valutas" :value="valuta">{{ index }}</option>
            </select>

            <label class="country-code__valuta">{{ converting }}</label>
        </div>

        <div class="converter__logo" aria-label="Bank logo">
            <svg aria-label="Bank logo" width="60" height="60" viewBox="0 0 85 85" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M35.8771 56.1071L33.0862 66.5196L26.2473 64.685L29.0346 54.276C24.8624 52.7373 20.9848 50.4948 17.5702 47.646L9.94498 55.2748L4.93352 50.2633L12.5623 42.6381C8.25587 37.4806 5.36237 31.2933 4.16498 24.6819L7.35248 24.0975C17.5851 31.4754 29.8849 35.4365 42.5 35.4167C55.6183 35.4167 67.7556 31.2198 77.6475 24.0975L80.835 24.6783C79.6392 31.2907 76.7469 37.4792 72.4412 42.6381L80.0664 50.2633L75.055 55.2748L67.4298 47.646C64.0152 50.4948 60.1376 52.7373 55.9654 54.276L58.7527 64.6885L51.9137 66.5196L49.1229 56.1071C44.7396 56.8582 40.2604 56.8582 35.8771 56.1071V56.1071Z" fill="#982FA9"/>
            </svg>

            <p aria-label="The name of the bank, myesBank">myesBank</p>
        </div>
	</main>

    <div v-if="error">
        {{ error }}
    </div>
</template>

<script>
	export default {
		data () {
	 		return {
				today: '',
                valutas: {},
                eurValuta: '',
                selectedValuta: 1,
                converting: '',
                error: ''
	 		}
	 	},

		created() {
		 	this.fetchValue();
		},

	 	methods: {
	 		async fetchValue() {
	 			const url = `https://cdn.jsdelivr.net/gh/fawazahmed0/currency-api@1/latest/currencies/eur.json`;
	 			const respond = await fetch(url);
                try {
                    await this.handleRespond(respond);
                } catch (error) {
                    this.error = error.message;
                }
            },
             
			async handleRespond(respond) {
				if(respond.status >= 200 && respond.status < 300) {
					const result  = await respond.json();
					this.today = result.date;
                    this.valutas = result.eur;
                    this.eurValuta = result.eur.eur;
				
					return true;
					
				} else {
					if(respond.status === 404) {
						throw new Error('Url ikke funnet!');
					}
					if(respond.status === 401) {
						throw new Error('Ikke authorisert!');
					}
					if(respond.status > 500) {
						throw new Error('Server error!');
					}
					throw new Error('Noe gikk galt!');
				}
			},

            calculate() {
                const output = this.eurValuta * this.selectedValuta;
                this.converting = output;
                this.converting = parseFloat(this.converting).toFixed(4);
            }
	 	}
	}
</script>

<!-- Kommenterer script
	1 Henter ut info fra api
	2 Sjekker om status er større eller lik 200 OG mindre enn 300
	2.1 Hvis status er det så hentes alle resultatene jeg trenger fra api
	2.2 Gir resultatene nye navn, som this.todat = result.date
	3 Hvis ikke 2 er true, så sjekkes det hvilken feilmelding som gjelder og skriver ut en errormelding
    4 Lager en variabel output som er euro valutaen ganget med den valutaen som er valgt
    4.1 Sier at this.converting bare skal ha 4 desimaler
-->

<style>
    .converter__title {
        width: 530px;
        text-align: center;
        margin: 40px;
        padding: 10px 0;
        border: 3px solid var(--foreground);
        border-radius: 7px;
        font-size: 50px;
    }

    @media screen and (max-width: 650px) {
        .converter__title {
            text-align: left;
            margin: 40px 0 0 40px;
            padding: 0;
            border: none;
            font-size: 30px;
        }
    }

    .converter__date {
        text-align: left;
        margin-left: 70px;
        font-size: 30px;
        font-weight: bold;
    }

    @media screen and (max-width: 650px) {
        .converter__date {
            font-size: 20px;
        }
    }

    .converter__euro, .converter__country-code {
        display: flex;
        margin: 50px 0px 0px 165px;
    }

    @media screen and (max-width: 650px) {
        .converter__euro, .converter__country-code {
            margin: 50px 0px 0px 50px;
        }
    }

    .euro__text {
        width: 115px;
        padding-left: 4px;
        font-weight: lighter;
    }

    .country-code__text, .euro__text {
        height: 40px;
        font-size: 30px;
        font-family: 'Courier New', Courier, monospace;
        background-color: var(--background);
        border: 2px solid var(--foreground);
        border-radius: 7px;
        color: var(--foreground);
    }

    .euro__calculate {
        margin-left: 10px;
    }

    .euro__valuta, .country-code__valuta {
        width: 130px;
        height: 40px;
        margin-left: 40px;
        border: 2px solid var(--foreground);
        border-radius: 7px;
        font-size: 30px;
        color: black;
    }

    @media screen and (max-width: 650px) {
        .euro__valuta, .country-code__valuta {
            margin-left: 10px;
        }
    }

    .converter__logo {
        display: flex;
        margin: 110px 0px 0px 40px;
    }
</style>
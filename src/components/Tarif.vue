<script setup>

	import { ref, reactive, watchEffect } from 'vue'
	import axios from 'axios'

	const loading = ref(false)
	const url = ref('https://www.cbr-xml-daily.ru/daily_json.js')
	const tarifOptions = ref(['Стандартный', 'Продвинутый'])
	const currencyOptions = ref(['Российский рубль'])
	const currencyCodes = ref(['RUB', 'KZT', 'CNY'])
	const currMultiply = ref([1])
	const periodOptions = ref([ 'В месяц', 'В год'])
	const periodValues = ref([
		[100, 150],
		[1000, 1400]
	])


	const state = reactive({
		tarif: tarifOptions.value[0],
		period: periodOptions.value[0],
		currency: currencyOptions.value[0],
		currencyName: currencyOptions.value[0],
		currencyNameEnd: 'рублей',
		price: periodValues.value[0][0],
		discount: 0
	})

	const setCurrencyName = (arg) => {
		let currNameEnd = arg.split(' ')[1]

		if (currNameEnd.match(/ь$/)) {
			state.currencyNameEnd = currNameEnd.slice(0, -1) + 'ей'
		} else {
			state.currencyNameEnd = currNameEnd
		}		
	}

	const setSelect = value => {

		value = event.target.value

		for (let i = 0; i < tarifOptions.value.length; i++) {
			for (let j = 0; j < currencyOptions.value.length; j++) {
				for (let k = 0; k < periodOptions.value.length; k++) {

					if (
						state.tarif === tarifOptions.value[i] && 
						state.currency === currencyOptions.value[j] && 
						state.period === periodOptions.value[k]
					) {

						setCurrencyName(currencyOptions.value[j])

						state.price = periodValues.value[k][i]
						state.price = state.price * currMultiply.value[j]

						const year = periodValues.value[1][i] * currMultiply.value[j]
						const monthToYear = periodValues.value[0][i] * currMultiply.value[j] * 12

						const procent = year / 100
						const diff = monthToYear - year
						state.discount = Math.round(diff / procent)

					}

				}
			}
		}

	}

	watchEffect(async () => {
		try {

			loading.value = true

			const { data } = await axios.get(url.value)

			currencyCodes.value.map( code => {
				Object.entries(data.Valute).map( item => {
					if (item[0] === code) {
						currencyOptions.value.push(item[1].Name)
						currMultiply.value.push(Math.round(item[1].Value))
					}
				})
			})		

		} catch (e) {
			console.warn(e)
			currencyOptions.value = []
			currMultiply.value = []
		} finally {
			loading.value = false
		}

	})

</script>

<template>

<div class="select-group">

	<div class="select-wrapper">

		<div class="select-item">
			<h3 class="select-title">Выбор тарифа</h3>
			<select
				class="select" 
				v-model="state.tarif"
				:value="state.tarif"
				@change="setSelect(state.tarif)"
			>
				<option 
					v-for="item,index in tarifOptions" 
					:value="item" 
					:key="index"
				>
					{{item}}
				</option>
			</select>			
		</div>

		<div class="select-item">
			<h3 class="select-title">Выбор валюты</h3>
			<select
				class="select" 
				v-model="state.currency" 
				:value="state.currency"
				@change="setSelect(state.currency)"
			>
				<option 
					v-for="item,index in currencyOptions" 
					:value="item" 
					:key="index"
				>
					{{item}}
				</option>
			</select>
		</div>

		<div class="select-item">
			<h3 class="select-title">Выбор периода оплаты</h3>
			<select
				class="select" 
				v-model="state.period" 
				:value="state.period" 
				@change="setSelect(state.period)"
			>
				<option 
					v-for="item,index in periodOptions"
					:value="item"
					:key="index" 
				>
					{{item}}	
				</option>
			</select>
		</div>
		
	</div>



		<h3 class="tarif-title">Тариф <span>{{state.tarif}}</span></h3>

		<div class="price-container">
			<p class="price-value">
				<span>Сумма </span>
				<span>{{state.price + ' ' + state.currencyNameEnd}}</span>
			</p>
			<p class="price-discount">
				<span>Скидка </span>
				<span>{{state.discount}} %</span>
			</p>
		</div>

</div>

  
</template>

<style scoped lang="scss">
	$fontSize: 24px;

	.select-group {
		margin-top: 50px;
		display: flex;
		flex-direction: column;
		justify-content: center;
		row-gap: 20px;
	}

	.select-wrapper {
		display: flex;
		align-items: center;
		column-gap: 30px;
	}

	.select-title {
		color: #fff;
		font-size: 20px;
		max-width: 350px;
	}

	.select {
		min-width: 250px;
		width: 100%;
		height: 50px;
		font-size: 18px;
	}

	.tarif-title {
		font-size: $fontSize;
		color: #fff;

		span {
			font-size: 26px;
			font-weight: 700;
			margin-left: 10px;
			border-bottom: 2px solid #fff;
			padding-bottom: 2px;
		}
	}

	.price-container {
		p {
			margin: 0;
			font-size: $fontSize	;
		}		
	}



</style>

<template>
	<NcAppContentList>
		<ul>
			<div class="listHeader">
				<NcTextField class="searchField" disabled :value.sync="search" label="Search" trailing-button-icon="close"
							 :show-trailing-button="search !== ''" @trailing-button-click="clearText">
					<Magnify :size="20" />
				</NcTextField>
			</div>

			<NcListItem v-for="(zaak, i) in zaken.results" :key="`${zaak}${i}`"
						:href="'/index.php/apps/dsonextcloud/zaken/' + zaak.id" :name="zaak?.omschrijving"
						:details="zaak?.einddatumGepland">
				<template #subname>
					{{ zaak?.zaaktype === 'http://localhost/api/ztc/v1/zaaktypen/a1748dd6-50a3-464d-b95e-554e87298ce9' ?
					'Aanmelding hondenbelasting' : (zaak?.zaaktype ?? 'Onbekend') }}
				</template>
			</NcListItem>

			<NcListItem v-for="(zaak, i) in zaken.results" :key="`${zaak}${i}`"
						:name="zaak?.omschrijving"
						:bold="false"
						:force-display-actions="true"
						:details="'1h'"
						:counter-number="44">
				:href="'/index.php/apps/dsonextcloud/zaken/' + zaak.id"
				<template #icon>
					<BriefcaseOutline disable-menu :size="44" user="janedoe" display-name="Jane Doe" />
				</template>
				<template #subname>
					{{ zaak?.zaaktype === 'http://localhost/api/ztc/v1/zaaktypen/a1748dd6-50a3-464d-b95e-554e87298ce9' ?
					'Aanmelding hondenbelasting' : (zaak?.zaaktype ?? 'Onbekend') }}
				</template>
				<template #actions>
					<NcActionButton>
						Button one
					</NcActionButton>
					<NcActionButton>
						Button two
					</NcActionButton>
					<NcActionButton>
						Button three
					</NcActionButton>
				</template>
			</NcListItem>
		</ul>
	</NcAppContentList>
</template>
<script>

import Vue from 'vue';
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';
import { BPaginationNav, BPagination } from 'bootstrap-vue'
import { TEMP_AUTHORIZATION_KEY } from '../../data/TempAuthKey';
import { NcListItem,NcListItemIcon, NcActionButton, NcAvatar, NcAppContentList, NcTextField } from '@nextcloud/vue';
import Magnify from 'vue-material-design-icons/Magnify';
import BriefcaseOutline from 'vue-material-design-icons/BriefcaseOutline';

Vue.component('b-pagination-nav', BPaginationNav)
Vue.component('b-pagination', BPagination)

Vue.use(Loading);

export default {
	name: "ZakenList",
	props: {
		msg: String,
	},
	components: {
		NcListItem,
		NcListItemIcon,
		NcActionButton,
		NcAvatar,
		NcAppContentList,
		NcTextField,
		BriefcaseOutline,
		Magnify
	},
	data() {
		return {
			zaken: '',
			search: '',
			fullPage: false,
			currentPage: 1,
			perPageLimit: 10,
		}
	},
	mounted() {
		this.fetchData()
	},
	methods: {
		fetchData(newPage) {
			let loader = this.$loading.show({
				container: this.fullPage ? null : this.$refs.table,
				canCancel: true,
				onCancel: this.onCancel,
				color: "#39870c",
				loader: "dots",
			});
			fetch(
				'https://api.test.common-gateway.commonground.nu/api/kic/v1/taken?' + new URLSearchParams({ page: newPage ?? this.currentPage, _limit: this.perPageLimit }),
				{
					method: 'GET',
					headers: {
						"Authorization": TEMP_AUTHORIZATION_KEY
					}
				},
			)
				.then((response) => {
					response.json().then((data) => {
						this.zaken = data
					})
					loader.hide()
				})
				.catch((err) => {
					console.error(err)
					loader.hide()
				})
		},
		onPageClick(event, page) {
			this.fetchData(page)
		},
		clearText() {
			this.search = ''
		}
	},
}
</script>
<style>
.listHeader {
	position: sticky;
	top: 0;
	z-index: 1000;
	background-color: var(--color-main-background);
	border-bottom: 1px solid var(--color-border);
}

.searchField {
	padding-inline-start: 65px;
	padding-inline-end: 20px;
	margin-block-end: 6px;
}
</style>

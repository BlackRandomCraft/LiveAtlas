<!--
  - Copyright 2022 James Lyne
  -
  - Licensed under the Apache License, Version 2.0 (the "License");
  - you may not use this file except in compliance with the License.
  - You may obtain a copy of the License at
  -
  - http://www.apache.org/licenses/LICENSE-2.0
  -
  - Unless required by applicable law or agreed to in writing, software
  - distributed under the License is distributed on an "AS IS" BASIS,
  - WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  - See the License for the specific language governing permissions and
  - limitations under the License.
  -->

<template>
	<button class="ui__element ui__button" type="button" :title="buttonTitle" :aria-expanded="modalVisible"
	        @click.prevent.stop="handleClick"
			@keydown.right.prevent.stop="handleClick">
		<SvgIcon :name="loggedIn ? 'logout' : 'login'"></SvgIcon>
	</button>
</template>

<script lang="ts">
import {computed, defineComponent} from "@vue/runtime-core";
import SvgIcon from "@/components/SvgIcon.vue";
import {useStore} from "@/store";
import {ActionTypes} from "@/store/action-types";
import {notify} from "@kyvg/vue3-notification";

import "@/assets/icons/login.svg";
import "@/assets/icons/logout.svg";

export default defineComponent({
	components: {SvgIcon},

	setup() {
		const store = useStore(),
			loggedIn = computed(() => store.state.loggedIn),
			buttonTitle = computed(() => loggedIn.value ? store.state.messages.logoutTitle : store.state.messages.loginTitle),
			modalVisible = computed(() => store.state.ui.visibleModal === 'login');

		const handleClick = async () => {
			const logoutSuccess = computed(() => store.state.messages.logoutSuccess),
				logoutError = computed(() => store.state.messages.logoutErrorUnknown);

			if (loggedIn.value) {
				try {
					await store.dispatch(ActionTypes.LOGOUT, undefined);
					notify(logoutSuccess.value);
				} catch(e) {
					notify(logoutError.value);
				}
			} else {
				await store.dispatch(ActionTypes.LOGIN, null)
			}
		};

		return {
			modalVisible,
			loggedIn,
			buttonTitle,

			handleClick
		}
	},
})
</script>

<script lang="ts">
	/**
	 * Dependence
	 */
	import PostsApi from '$lib/api/methods/posts';
	import { getReasonPhrase } from 'http-status-codes';

	/**
	 * Components
	 */
	import Feed from '$lib/components/feed/Feed.svelte';
	import Loader from '$lib/components/shared/Loader.svelte';
	import ErrorComponent from '$lib/components/shared/Error.svelte';

	/**
	 * Constants for API URL and local storage key
	 */
	const API__URL = 'https://jsonplaceholder.typicode.com/posts';
	const STORAGE__KEY = 'cachedPosts';

	/**
	 * Load data
	 * @description Метод для получения постов
	 */
	const loadData = async () => {
		try {
			const response = await PostsApi.getAll();

			if (!response.success) {
				throw new Error(getReasonPhrase(response.data.code));
			}

			alert('Данные загружены из API.');

			// Save posts to local storage
			localStorage.setItem(STORAGE__KEY, JSON.stringify(response.data));

			return response.data;
		} catch (error) {
			// Try to load posts from local storage
			const cachedPosts = localStorage.getItem(STORAGE__KEY);
			if (cachedPosts) {
				alert('Данные загружены из кеша.');
				return JSON.parse(cachedPosts);
			} else {
				throw new Error('Нет данных для отображения.');
			}
		}
	};

</script>

{#await loadData()}
	<Loader />
{:then posts}
	<Feed {posts} />
{:catch e}
	<ErrorComponent>
		{e}
	</ErrorComponent>
{/await}

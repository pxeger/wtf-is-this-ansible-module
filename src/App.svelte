<style>
	* {
		box-sizing: border-box;
	}
	a {
		color: inherit;
		text-decoration: underline;
	}
	main {
		max-width: 60em;
		margin: -0.5em auto 1em;
		padding: 0 0.5em;
	}
	code {
		background: #0001;
		padding: 0 0.3em;
	}
	footer {
		justify-content: center;
		gap: 0.5em;
	}
	#search {
		margin-top: 0.5em;
	}
	#input {
		width: 100%;
	}
	.flex {
		display: flex;
	}
	.flex-grow {
		flex-grow: 1;
	}
	input[type=submit]:disabled {
		cursor: not-allowed;
	}
	.centered {
		width: 100%;
		text-align: center;
	}
	.result {
		padding: 0 1em;
		overflow: auto;
		transition: 0.2s;
	}
	.result:hover {
		background: #f4f4f4;
	}
	.result span {
		padding: 0.2em;
		border-radius: 0.3em;
	}
</style>

<script>
	import data from '../output.json';
	import Fuse from 'fuse.js';

	// generate random placeholder
	const choose = array => array[Math.floor(Math.random() * array.length)];
	const random_placeholder = choose([
		// a bunch of random module names as examples
		'authorized_key',
		'yum',
		'postgresql_db',
		'assemble',
		'locale_gen',
		'ec2_vpc_net',
		'xenserver_guest_facts',
		'telegram',
		'gitlab_project_variable',
		'win_say',
		'import_playbook',
		'wakeonlan',
		'hpilo_info'
	]);

	window.disclaimer = `\
Module names and descriptions are copyright by their respective authors used under open source licences. They may also\
be registered trademarks of their respective owners. To the extent permitted by law, this website is provided "as is",\
with no warranty, express nor implied. The content is generated externally and automatically and I disclaim all\
responsibility for it. Use at your own risk!`

	const search = (new URL(document.location)).searchParams.get('search');
	const results = search && (
		new Fuse(data, {
			keys: ['name', 'description']
		})
	).search(search);

	console.log(results);

	let textValue;
	$: textValue = search || null;
	function handleTextboxInput (event) {
		textValue = event.target.value;
	}
</script>

<main>
	<h1>"WTF is this Ansible module?"</h1>
	<p>
		With the move to collections in Ansible 2.10, development of the most common modules included by default with
		older versions has moved to separate GitHub repositories (mostly under the <code>ansible-collections</code>
		organisation) because they are part of separate collections now. Many have moved to
		<code>community.general</code>, but there are some in <code>ansible.posix</code> or any number of other
		repositories. This is a tool to help you work out the "FQCN" of a module, or so that you know where to file
		issues with a module.
	</p>
	<hr>
	<form method="GET" action="./">
		<label for="search">Search Query</label>
		<div class="flex" id="search">
			<input
				type="text"
				name="search"
				placeholder={random_placeholder}
				value={search || null}
				on:input={handleTextboxInput}
				id="input"
				aria-required="true"
				aria-label="Search Query"
				role="textbox"
			>
			<input
				type="submit"
				value="Go"
				aria-label="Go"
				disabled={!textValue}
				role="button"
				aria-disabled={!textValue}
			>
		</div>
	</form>
	{#if search}
		<div id="results">
			<h2>{results.length} Result{results.length === 1 ? '' : 's'}</h2>
			{#if results.length === 0}
				<p>Sorry, nothing was found!</p>
			{/if}
			{#each results as result}
				<div class="result">
					<h3 class="flex">
						<div class="flex-grow">
							<code>{result.item.name}</code>
							&nbsp;from&nbsp;
							<a href="https://galaxy.ansible.com/{result.item.collection.replaceAll('.', '/')}">
								{result.item.collection}
							</a>
						</div>
						<span class={result.item.content_type.replaceAll('_', '-')}>
							{@html result.item.content_type.replaceAll('_', '&nbsp;')}
						</span>
					</h3>
					<p>{result.item.description}</p>
				</div>
			{/each}
		</div>
	{/if}
	<hr>
	<footer class="flex">
		<span><a href="https://github.com/pxeger/wtf-is-this-ansible-module/blob/master/LICENCE.txt">Licence</a></span>
		<span>•</span>
		<span><a href="https://github.com/pxeger/wtf-is-this-ansible-module">Source code</a></span>
		<span>•</span>
		<span><a href="mailto:_@pxeger.com">Contact</a></span>
		<span>•</span>
		<span><a href="https://www.netlify.com">Hosting</a></span>
		<span>•</span>
		<span><a href="javascript:alert(disclaimer)">Disclaimer</a></span>
	</footer>
</main>

<script>
	import Fa from 'svelte-fa';

	import { faX, fa0 } from '@fortawesome/free-solid-svg-icons';

	const X = 'X';
	const Zero = '0';

	let currentPlayer = Zero;

	const matrix = [
		{ value: '' },
		{ value: '' },
		{ value: '' },
		{ value: '' },
		{ value: '' },
		{ value: '' },
		{ value: '' },
		{ value: '' },
		{ value: '' }
	];

	const combinationsToCheckWinner = [
		[0, 1, 2],
		[3, 4, 5],
		[6, 7, 8],

		[0, 4, 8],
		[2, 4, 6],

		[0, 3, 6],
		[1, 4, 7],
		[2, 5, 8]
	];

	$: matrix, calculateWinner();
	$: matrix, flipCurrentPlayer();

	function makeMove(value) {
		const index = +value.target.id;
		const valueAtIndex = matrix.at(index);
		matrix[index].value = setValue(valueAtIndex);
	}

	function setValue({ value }) {
		if (currentPlayer == X) {
			return value ? '' : X;
		}

		if (currentPlayer == Zero) {
			return value ? '' : Zero;
		}
	}

	function calculateWinner() {
		let isXWinner = [];
		let isZeroWinner = [];

		combinationsToCheckWinner.forEach((combination) => {
			const valuesAtCombination = [];

			for (let i = 0; i < combination.length; i++) {
				const valueAtIndex = matrix[combination[i]];
				valuesAtCombination.push(valueAtIndex);
			}

			isXWinner.push(valuesAtCombination.every((x) => x.value !== '' && x.value === X));

			isZeroWinner.push(valuesAtCombination.every((x) => x.value !== '' && x.value === Zero));
		});

		const isX = isXWinner.some((x) => x === true);
		if (isX) {
			alert(`player X is winner`);
		}

		const isZero = isZeroWinner.some((x) => x === true);
		if (isZero) {
			alert(`player Zero is winner`);
		}

		if (isX || isZero) {
			const randomInRange = (min, max) => Math.random() * (max - min) + min;

			const duration = 15 * 1000,
				animationEnd = Date.now() + duration,
				defaults = { startVelocity: 30, spread: 360, ticks: 60, zIndex: 0 };

			const interval = setInterval(function () {
				const timeLeft = animationEnd - Date.now();

				if (timeLeft <= 0) {
					return clearInterval(interval);
				}

				const particleCount = 50 * (timeLeft / duration);

				// since particles fall down, start a bit higher than random
				confetti(
					Object.assign({}, defaults, {
						particleCount,
						origin: { x: randomInRange(0.1, 0.3), y: Math.random() - 0.2 }
					})
				);
				confetti(
					Object.assign({}, defaults, {
						particleCount,
						origin: { x: randomInRange(0.7, 0.9), y: Math.random() - 0.2 }
					})
				);
			}, 250);
		}
	}

	function flipCurrentPlayer() {
		currentPlayer = currentPlayer == X ? Zero : X;
	}
</script>

<div class="grid place-items-center h-screen bg-gradient-to-r bg-slate-900">
	<div class="max-w-sm rounded-md overflow-hidden shadow-lg bg-gradient-to-bl from-blue-900">
		<div class="px-6 py-4">
			<p class="text-center font-bold text-2xl mb-2 text-blue-300/70">Tic tac toe</p>
			<p class="font-bold text-xl text-sky-100">Current Player: {currentPlayer}</p>
			<div
				class="mt-6 text-lg font-medium grid grid-cols-3 grid-rows-3 gap-6 content-stretch place-content-center"
			>
				{#each matrix as item}
					<button
						id={matrix.indexOf(item)}
						on:click|preventDefault={(event) => makeMove(event)}
						class="flex items-center justify-center bg-white/30 w-24 h-24 rounded-md font-extrabold text-5xl shadow-lg text-center uppercase text-sky-100 hover:from-blue-200 hover:to-green-200 hover:bg-gradient-to-tr hover:text-sky-900 hover:ease-in-out duration-300"
					>
						{#if item.value === 'X'}
							<Fa icon={faX} class="  hover:outline-none pointer-events-none" />
						{/if}

						{#if item.value === '0'}
							<Fa icon={fa0} class="  hover:outline-none pointer-events-none" />
						{/if}
					</button>
				{/each}
			</div>

			<div class="mt-6">
				<!-- <button
					class="flex items-center justify-center bg-white/30 w-40 h-10 rounded-md font-extrabold text-1xl shadow-lg text-center uppercase text-sky-100 hover:from-blue-200 hover:to-green-200 hover:bg-gradient-to-tr hover:text-sky-900 hover:ease-in-out duration-300"
					>Confirm move</button
				> -->
			</div>
		</div>
	</div>
</div>

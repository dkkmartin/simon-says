<script lang="ts">
	import { randomInt } from '$lib'
	import { onMount } from 'svelte'

	const rows = 4
	const cols = 4
	let board = Array.from({ length: rows }, () => Array(cols).fill(0))

	const handleKeyDown = (e: KeyboardEvent) => {
		if (e.key === 'ArrowDown') {
			moveRowDown()
		} else if (e.key === 'ArrowUp') {
			moveRowUp()
		} else if (e.key === 'ArrowLeft') {
			moveColLeft()
		} else if (e.key === 'ArrowRight') {
			moveColRight()
		}
		addRandomNumber()
	}

	function refreshBoard() {
		board = [...board]
	}

	function getColorClass(cell: number): string {
		if (cell === 2) return 'bg-gray-200'
		if (cell === 4) return 'bg-gray-400'
		if (cell === 8) return 'bg-yellow-200'
		if (cell === 16) return 'bg-yellow-400'
		if (cell === 32) return 'bg-orange-200'
		if (cell === 64) return 'bg-orange-400'
		if (cell === 128) return 'bg-red-200'
		if (cell === 256) return 'bg-red-400'
		if (cell === 512) return 'bg-purple-200'
		if (cell === 1024) return 'bg-purple-400'
		if (cell === 2048) return 'bg-green-200'
		return 'bg-white'
	}

	function addRandomNumber() {
		let emptyPositions = []
		for (let row = 0; row < rows; row++) {
			for (let col = 0; col < cols; col++) {
				if (board[row][col] === 0) {
					emptyPositions.push({ row, col })
				}
			}
		}

		if (emptyPositions.length > 0) {
			const { row, col } = emptyPositions[randomInt(emptyPositions.length - 1)]
			board[row][col] = 2
			board = [...board]
		}
	}

	function moveRowUp() {
		for (let col = 0; col < cols; col++) {
			let merged = Array(rows).fill(false)
			for (let row = 1; row < rows; row++) {
				if (board[row][col] !== 0) {
					let newRow = row
					while (newRow > 0 && board[newRow - 1][col] === 0) {
						newRow--
					}
					if (newRow > 0 && board[newRow - 1][col] === board[row][col] && !merged[newRow - 1]) {
						board[newRow - 1][col] *= 2
						board[row][col] = 0
						merged[newRow - 1] = true
					} else if (newRow !== row) {
						board[newRow][col] = board[row][col]
						board[row][col] = 0
					}
				}
			}
		}
		refreshBoard()
	}

	function moveRowDown() {
		for (let col = 0; col < cols; col++) {
			let merged = Array(rows).fill(false)
			for (let row = rows - 2; row >= 0; row--) {
				if (board[row][col] !== 0) {
					let newRow = row
					while (newRow < rows - 1 && board[newRow + 1][col] === 0) {
						newRow++
					}
					if (
						newRow < rows - 1 &&
						board[newRow + 1][col] === board[row][col] &&
						!merged[newRow + 1]
					) {
						board[newRow + 1][col] *= 2
						board[row][col] = 0
						merged[newRow + 1] = true
					} else if (newRow !== row) {
						board[newRow][col] = board[row][col]
						board[row][col] = 0
					}
				}
			}
		}
		refreshBoard()
	}

	function moveColLeft() {
		for (let row = 0; row < rows; row++) {
			let merged = Array(cols).fill(false)
			for (let col = 1; col < cols; col++) {
				if (board[row][col] !== 0) {
					let newCol = col
					while (newCol > 0 && board[row][newCol - 1] === 0) {
						newCol--
					}
					if (newCol > 0 && board[row][newCol - 1] === board[row][col] && !merged[newCol - 1]) {
						board[row][newCol - 1] *= 2
						board[row][col] = 0
						merged[newCol - 1] = true
					} else if (newCol !== col) {
						board[row][newCol] = board[row][col]
						board[row][col] = 0
					}
				}
			}
		}
		refreshBoard()
	}

	function moveColRight() {
		for (let row = 0; row < rows; row++) {
			let merged = Array(cols).fill(false)
			for (let col = cols - 2; col >= 0; col--) {
				if (board[row][col] !== 0) {
					let newCol = col
					while (newCol < cols - 1 && board[row][newCol + 1] === 0) {
						newCol++
					}
					if (
						newCol < cols - 1 &&
						board[row][newCol + 1] === board[row][col] &&
						!merged[newCol + 1]
					) {
						board[row][newCol + 1] *= 2
						board[row][col] = 0
						merged[newCol + 1] = true
					} else if (newCol !== col) {
						board[row][newCol] = board[row][col]
						board[row][col] = 0
					}
				}
			}
		}
		refreshBoard()
	}

	onMount(() => {
		addRandomNumber()
		addRandomNumber()
		window.addEventListener('keydown', handleKeyDown)
		return () => window.removeEventListener('keydown', handleKeyDown)
	})
</script>

<main class="flex h-screen w-screen items-center justify-center">
	<section class="grid grid-cols-4">
		{#each board as row}
			{#each row as cell}
				<div
					class={`flex h-32 w-32 items-center justify-center border border-black ${getColorClass(cell)}`}
				>
					{#if cell !== 0}
						<span class="bold text-3xl">{cell}</span>
					{/if}
				</div>
			{/each}
		{/each}
	</section>
</main>

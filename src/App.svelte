<script lang="ts">
  import X from "./components/X.svelte";
  import O from "./components/O.svelte";

  const BASE_CELL_SIZE = 50;
  let cellSize = BASE_CELL_SIZE;
  let rowCount = Math.floor(innerHeight / BASE_CELL_SIZE);
  let colCount = Math.floor(innerWidth / BASE_CELL_SIZE);
  let turn = 1;
  let isGameStarted = false;

  let winner = 0;
  let winPositions = [];

  let board = new Array(rowCount).fill("").map(() => ({
    id: Math.random().toString(36).slice(2),
    value: new Array(colCount).fill("").map(() => ({
      id: Math.random().toString(36).slice(2),
      value: 0,
    })),
  }));
  type BoardType = typeof board;

  const updateCellSize = () => {
    if (isGameStarted) {
      cellSize = Math.min(
        Math.floor(innerHeight / rowCount),
        Math.floor(innerWidth / colCount)
      );
    } else {
      rowCount = Math.floor(innerHeight / BASE_CELL_SIZE);
      colCount = Math.floor(innerWidth / BASE_CELL_SIZE);
      board = new Array(rowCount).fill("").map(() => ({
        id: Math.random().toString(36).slice(2),
        value: new Array(colCount).fill("").map(() => ({
          id: Math.random().toString(36).slice(2),
          value: 0,
        })),
      }));
    }
  };

  addEventListener("resize", () => {
    updateCellSize();
  });

  const handleCellClicked = (
    rowId: string,
    colId: string,
    rowIndex: number,
    colIndex: number
  ) => {
    isGameStarted = true;

    if (winner) return;

    const clone = JSON.parse(JSON.stringify(board)) as BoardType;
    const existingRow = clone.find((row) => row.id === rowId);
    const existingCol = existingRow.value.find((col) => col.id === colId);
    if (existingCol.value !== 0) return;
    existingCol.value = turn;
    board = clone;

    turn = (turn + 1) % 2 === 0 ? 2 : 1;

    if (rowIndex === 0) {
      rowCount++;
      updateCellSize();
      board = [
        {
          id: Math.random().toString(36).slice(2),
          value: new Array(colCount).fill("").map(() => ({
            id: Math.random().toString(36).slice(2),
            value: 0,
          })),
        },
        ...board,
      ];
    }
    if (colIndex === 0) {
      colCount++;
      updateCellSize();
      board = board.map((row) => ({
        id: row.id,
        value: [
          {
            id: Math.random().toString(36).slice(2),
            value: 0,
          },
          ...row.value,
        ],
      }));
    }
    if (rowIndex === board.length - 1) {
      rowCount++;
      updateCellSize();
      board = [
        ...board,
        {
          id: Math.random().toString(36).slice(2),
          value: new Array(colCount).fill("").map(() => ({
            id: Math.random().toString(36).slice(2),
            value: 0,
          })),
        },
      ];
    }
    if (colIndex === board[0].value.length - 1) {
      colCount++;
      updateCellSize();
      board = board.map((row) => ({
        id: row.id,
        value: [
          ...row.value,
          {
            id: Math.random().toString(36).slice(2),
            value: 0,
          },
        ],
      }));
    }

    const result = checkWin(board);

    if (result) {
      winner = result.winner;
      winPositions = result.winPositions;
    }
  };

  const animateCell = (node, params) => {
    return {
      delay: params.delay || 0,
      duration: params.duration || 300,
      css: (t) => {
        return `${params.height ? `max-height: ${t * params.size}px; ` : ""} ${
          params.width ? `max-width: ${t * params.size}px; ` : ""
        }`;
      },
    };
  };

  const checkWin = (board: BoardType) => {
    // Horizontal check
    for (let row = 0; row < board.length; row++) {
      for (let col = 0; col < board[row].value.length - 5; col++) {
        const cells = board[row].value.slice(col, col + 5);
        if (
          cells.every((item) => item.value === cells[0].value) &&
          cells[0].value !== 0
        ) {
          return {
            winner: cells[0].value,
            winPositions: cells.map((_, index) => ({
              row,
              col: col + index,
            })),
          };
        }
      }
    }

    // Vertical check
    for (let col = 0; col < board[0].value.length; col++) {
      for (let row = 0; row < board.length - 5; row++) {
        const cells = new Array(5)
          .fill("")
          .map((_, index) => board[row + index].value[col]);
        if (
          cells.every((item) => item.value === cells[0].value) &&
          cells[0].value !== 0
        ) {
          return {
            winner: cells[0].value,
            winPositions: cells.map((_, index) => ({
              row: row + index,
              col: col,
            })),
          };
        }
      }
    }

    // Back slash
    for (let row = 0; row < board.length - 5; row++) {
      for (let col = 0; col < board[row].value.length - 5; col++) {
        const cells = new Array(5)
          .fill("")
          .map((_, index) => board[row + index].value[col + index]);
        if (
          cells.every((item) => item.value === cells[0].value) &&
          cells[0].value !== 0
        ) {
          return {
            winner: cells[0].value,
            winPositions: cells.map((_, index) => ({
              row: row + index,
              col: col + index,
            })),
          };
        }
      }
    }
    // Forward slash
    for (let row = 0; row < board.length - 5; row++) {
      for (let col = 5; col < board[0].value.length; col++) {
        const cells = new Array(5)
          .fill("")
          .map((_, index) => board[row + index].value[col - index]);
        if (
          cells.every((item) => item.value === cells[0].value) &&
          cells[0].value !== 0
        ) {
          return {
            winner: cells[0].value,
            winPositions: cells.map((_, index) => ({
              row: row + index,
              col: col - index,
            })),
          };
        }
      }
    }

    return null;
  };
</script>

<main class="h-screen w-full flex justify-center items-center">
  <div
    class="board flex flex-col items-stretch"
    style="font-size: {cellSize}px;"
  >
    {#each board as row, rowIndex (row.id)}
      <div
        class="flex justify-center {rowIndex === 0
          ? 'items-end'
          : 'items-start'}"
      >
        {#each row.value as col, colIndex (col.id)}
          <div
            transition:animateCell={{
              size: cellSize,
              height: rowIndex === 0 || rowIndex === board.length - 1,
              width: colIndex === 0 || colIndex === row.value.length - 1,
            }}
            on:click={() =>
              handleCellClicked(row.id, col.id, rowIndex, colIndex)}
            class="overflow-hidden"
            style="width: {cellSize}px; height: {cellSize}px;"
          >
            <div
              class="w-full h-full transition duration-300 {winner &&
              !winPositions.some(
                (item) => item.row === rowIndex && item.col === colIndex
              )
                ? 'scale-[60%] opacity-60'
                : ''}"
            >
              {#if col.value === 1}
                <X />
              {:else if col.value === 2}
                <O />
              {/if}
            </div>
          </div>
        {/each}
      </div>
    {/each}
  </div>
</main>

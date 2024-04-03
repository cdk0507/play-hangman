<script lang="ts">
import { defineComponent } from "vue";
import CAlert from "../components/CAlert.vue";
import CLoading from "../components/CLoading.vue";

export interface IKeyProps {
	letter: string;
	isPass: boolean | undefined;
}

export default defineComponent({
	name: "Hanman",
	components: { CAlert, CLoading },
	props: {},
	data() {
		this.getDate();
		const answerArr = [
			"apple",
			"banana",
			"Orange",
			"Grape",
			"Strawberry",
			"Mango",
			"Pineapple",
			"Watermelon",
			"cherry",
			"kiwi",
		];
		const answerI = Math.floor(Math.random() * answerArr.length);
		const answer = answerArr[answerI].toUpperCase();
		const answerTemp = Array.from(answer, () => "");
		const alphabetArr = Array.from({ length: 26 }, (_, index) =>
			String.fromCharCode("A".charCodeAt(0) + index)
		).map((d) => {
			return {
				letter: d,
				isPass: undefined,
			};
		});

		const initialKeyboardValues = [
			{
				id: 1,
				keys: ["q", "w", "e", "r", "t", "y", "u", "i", "o", "p"],
			},
			{
				id: 2,
				keys: ["a", "s", "d", "f", "g", "h", "j", "k", "l"],
			},
			{
				id: 3,
				keys: ["z", "x", "c", "v", "b", "n", "m"],
			},
		];

		const keyboardGroup = initialKeyboardValues.map((row) => {
			return {
				id: row.id,
				keys: row.keys.map((key) => {
					return {
						letter: key,
						isPass: undefined,
					};
				}),
			};
		});

		return {
			incorrectAttempts: 0,
			answer: answer,
			answerTemp: answerTemp,
			alphabet: alphabetArr,
			keyboardGroup: keyboardGroup,
			ctx: {} as any,
			params: {
				text: "정답입니다.",
			},
			isAlert: false,
			canvas: {} as any,
			isLoading: true,
		};
	},
	mounted() {
		this.canvas = this.$refs.hangmanCanvas as any; // ref로부터 canvas 요소 가져오기
		if (!this.canvas) {
			return;
		}
		this.ctx = this.canvas.getContext("2d"); // 옵셔널 체이닝 연산자 사용
		this.ctx.strokeStyle = "white";
		this.ctx.moveTo(20, 0);
		this.ctx.lineTo(20, 200);
		this.ctx.stroke();
	},
	methods: {
		async getDate(): Promise<any> {
			try {
				this.isLoading = true;
				const response = (await fetch(
					"https://main--taupe-beijinho-5f10a6.netlify.app/.netlify/functions/rand-word",
					{
						method: "get",
					}
				).then((d) => d.json())) as any;
				console.log(response);
				if (response.success !== true) {
					this.isLoading = false;
					return;
				}

				this.answer = response.data.toUpperCase();
				this.answerTemp = Array.from(this.answer, () => "");
				this.isLoading = false;
			} catch (e) {
				this.isLoading = false;
			}
		},

		drawHangman() {
			// 캔버스 초기화
			this.ctx.clearRect(0, 0, this.ctx.canvas.width, this.ctx.canvas.height);
			this.ctx.beginPath();
			this.ctx.moveTo(20, 0);
			this.ctx.lineTo(20, 200);
			this.ctx.stroke();

			if (this.incorrectAttempts >= 1) {
				// 긴줄
				this.ctx.beginPath();
				this.ctx.moveTo(20, 0);
				this.ctx.lineTo(150, 0);
				this.ctx.stroke();
			}

			if (this.incorrectAttempts >= 2) {
				// 짧은 줄
				this.ctx.beginPath();
				this.ctx.moveTo(100, 0);
				this.ctx.lineTo(100, 20);
				this.ctx.stroke();
			}

			if (this.incorrectAttempts >= 3) {
				// Head
				this.ctx.beginPath();
				this.ctx.arc(100, 40, 20, 0, 2 * Math.PI);
				this.ctx.stroke();
				this.ctx.fillStyle = "white";
				this.ctx.fill();
			}

			if (this.incorrectAttempts >= 4) {
				// Body
				this.ctx.beginPath();
				this.ctx.moveTo(100, 60);
				this.ctx.lineTo(100, 120);
				this.ctx.stroke();
			}

			if (this.incorrectAttempts >= 5) {
				// Left Arm
				this.ctx.beginPath();
				this.ctx.moveTo(100, 70);
				this.ctx.lineTo(60, 90);
				this.ctx.stroke();
			}

			if (this.incorrectAttempts >= 6) {
				// Right Arm
				this.ctx.beginPath();
				this.ctx.moveTo(100, 70);
				this.ctx.lineTo(140, 90);
				this.ctx.stroke();
			}

			if (this.incorrectAttempts >= 7) {
				// Left Leg
				this.ctx.beginPath();
				this.ctx.moveTo(100, 120);
				this.ctx.lineTo(70, 160);
				this.ctx.stroke();
			}

			if (this.incorrectAttempts >= 8) {
				// Right Leg
				this.ctx.beginPath();
				this.ctx.moveTo(100, 120);
				this.ctx.lineTo(130, 160);
				this.ctx.stroke();
			}
		},

		checkAnswer(data: IKeyProps) {
			[...this.answer].filter((d, i) => {
				if (data.letter.toLowerCase() === d.toLowerCase()) {
					data.isPass = true;
					this.answerTemp[i] = d;
				}
			});

			if (!data.isPass) {
				data.isPass = false;
				this.incorrectAttempts++;
				this.drawHangman();
				if (this.incorrectAttempts === 8) {
					this.params.text = "실패했습니다.";
					this.isAlert = true;
				}
				return;
			}
			if (this.answerTemp.join("") === this.answer) {
				this.params.text = "정답입니다.";
				this.isAlert = true;
			}
		},

		resetCanvas() {
			this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
			this.ctx.beginPath();
			this.ctx.moveTo(20, 0);
			this.ctx.lineTo(20, 200);
			this.ctx.stroke();
			this.incorrectAttempts = 0;
		},

		resetKeyboard() {
			this.keyboardGroup.forEach((group) => {
				group.keys.forEach((key: IKeyProps) => {
					if (key.hasOwnProperty("isPass")) {
						key.isPass = undefined;
					}
				});
			});
		},

		confirm(data: boolean) {
			this.isAlert = !data;
			this.resetCanvas();
			this.resetKeyboard();
			this.getDate();
		},
	},
});
</script>

<template>
	<c-loading v-show="isLoading" />
	<CAlert v-bind:params="params" v-if="isAlert" @confirm="confirm" />
	<section class="mt-10 game-section">
		<div class="py-10">
			<div class="hangman-container">
				<canvas class="mx-auto" ref="hangmanCanvas" width="150" height="200"></canvas>
			</div>
		</div>
		<div class="input-box mb-8 mt-5">
			<input
				type="text"
				class="answer-input"
				disabled
				v-for="(a, index) in answerTemp"
				v-bind:value="a"
				:key="index" />
		</div>
		<div class="keyboard">
			<div class="keyboard-row" v-for="group in keyboardGroup">
				<button
					v-for="d in group.keys"
					:key="d.letter"
					:class="[{ fail: d.isPass === false }, { pass: d.isPass }]"
					:disabled="d.isPass === false || d.isPass"
					v-on:click="checkAnswer(d)">
					{{ d.letter }}
				</button>
			</div>
		</div>
	</section>
</template>

<style scoped lang="scss">
.game-section {
	margin: 0 auto;
	max-width: 500px;

	.hangman-container {
		border-bottom: 1px solid #fff;
	}
}
.input-box {
	display: flex;
	flex-wrap: wrap;
	justify-content: center;
}
.answer-input {
	margin: 0 6px;
	padding: 6px;
	border-bottom: 2px solid #3a3a3c;
	max-width: 8%;
	font-size: 30px;
	font-weight: bold;
	text-align: center;
	background-color: transparent;
}
.keyboard {
	&-row {
		margin-bottom: 8px;
		display: flex;
		justify-content: center;
	}

	button {
		padding: 0;
		width: 43px;
		height: 58px;
		background-color: #818384;
		font-size: 20px;
		font-weight: bold;
		color: #f8f8f8;
		border-radius: 4px;

		&:not(:last-child) {
			margin-right: 6px;
		}
		&:hover {
			background-color: #696969;
		}

		&.fail {
			background-color: gray;
			@extend .vibration;
		}
		&.fail {
			background-color: #3a3a3c;
		}
		&.pass {
			background-color: #538d4e;
			@extend .vibration;
		}
	}
}
.vibration {
	animation-name: vibration;
	animation-duration: 0.15s;
	animation-iteration-count: 3;
}
@keyframes vibration {
	from {
		transform: rotate(5eg);
	}
	to {
		transform: rotate(-5deg);
	}
}
</style>

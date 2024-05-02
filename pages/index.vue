<template>
  <div class="app">
    <div
      v-for="(item, index) in verticalList"
      :key="index"
      class="vertical-list"
      ref="verticalList"
      :data-index="index"
    >
      <div
        v-for="(number, subIndex) in item.numbers"
        :key="subIndex"
        class="horizontal-list"
      >
        <div class="square">
          {{ number.number }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      VERTICAL_ELEMENTS: 100, //можно поставить желаемое кол-во элементов
      HORISONTAL_ELEMENTS: 10, //можно поставить желаемое кол-во элементов
      RANDOM: 100,
      verticalList: [],
      destroyInterval: undefined,
    };
  },
  mounted() {
    this.generateVerticalList();
    this.destroyInterval = setInterval(() => {
      this.updateNumbers();
    }, 5000);
  },
  beforeDestroy() {
    clearInterval(this.destroyInterval);
  },
  computed: {
    generateHorizontalNumber() {
      return Math.floor(Math.random() * this.HORISONTAL_ELEMENTS) + 1;
    },
    generateVerticalNumber() {
      return Math.floor(Math.random() * this.VERTICAL_ELEMENTS) + 1;
    },
  },
  methods: {
    generateRandomNumber() {
      return Math.floor(Math.random() * this.RANDOM) + 1;
    },
    generateVerticalList() {
      const list = [];
      for (let i = 0; i < this.generateVerticalNumber; i++) {
        const horizontalList = [];
        for (let j = 0; j < this.generateHorizontalNumber; j++) {
          horizontalList.push({ number: this.generateRandomNumber() });
        }
        list.push({ numbers: horizontalList });
      }
      this.verticalList = list;
    },
    updateNumbers() {
      const screenWidth = screen.availWidth;
      console.log("ggss", screenWidth);

      // пполучаем список индексов, которые находятся в пределах видимой ширины экрана
      const validVerticalIndices = this.verticalList
        .map((_, index) => index)
        .filter((index) => {
          const element = this.$refs.verticalList[index];
          const rect = element.getBoundingClientRect();
          return rect.left >= 0 && rect.right <= screenWidth;
        });
      // Выбираем случайный индекс из списка валидных индексов
      const randomVerticalIndex =
        validVerticalIndices[
          Math.floor(Math.random() * validVerticalIndices.length)
        ];

      const randomHorizontalIndex = Math.floor(
        Math.random() * this.verticalList[randomVerticalIndex].numbers.length
      );

      this.verticalList[randomVerticalIndex].numbers[
        randomHorizontalIndex
      ].number = this.generateRandomNumber();
    },
  },
};
</script>
<style scoped>
.app {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

.vertical-list {
  margin-right: 20px;
}

.horizontal-list {
  margin-bottom: 10px;
}

.square {
  width: 30px;
  height: 30px;
  border: 2px solid #000000;
  border-radius: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: all 0.3s ease;
}

.square:hover {
  transform: scale(0.8);
}
</style>

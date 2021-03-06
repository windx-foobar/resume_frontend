<template>
  <div class="marks">
    <div class="sh">
      <span class="tag" v-for="item in tags" @click="removeTag(item)">{{ item }} [x]</span>
      <input
        v-if="Name && List !== null"
        v-model="searchString"
        type="text"
        :placeholder="`Выберите ${ Name }`"
        @focus="ShowList = true"
        ref="InputRef"
      />
    </div>
    <ul v-if="ShowList && Name && List !== null" class="list">
      <li><b>Выберите {{ Name }}:</b></li>
      <li v-for="item in List" @click="addTag(item)">{{ item }}</li>
      <li @click="ShowList = false">[закрыть список]</li>
    </ul>
  </div>
</template>

<script>
// Компоненты

// Vuex

// Остальное

export default {
   name: 'Marks',
   data() {
      return {
         showList: false,
         tags: [],
         marks: ['Mercedes-Benz', 'шкода'],
         years: [2017, 2018, 2019, 2020, 2021],
         body: ['седан', 'купе'],
         list: ['B-Class', 'CLA', 'GLC-Class'],
         names: [
            'марку',
            'год',
            'кузов',
            'модификацию'
         ],
         searchString: ''
      };
   },
   computed: {
      /**
       * Вычисляемое наименование листа
       */
      Name() {
         return this.names[this.tags.length];
      },

      /**
       * Вычисляемый отфильтрованный лист
       */
      List() {
         return this.filterList();
      },

      /**
       * Вычисляемый флаг показывания листа
       */
      ShowList: {
         get() {
            if (this.isMaxLength()) {
               return false;
            }

            return this.showList;
         },
         set(value) {
            this.showList = value;
         }
      }
   },
   methods: {
      /**
       * Удаление тэга
       *
       * @param {String|Number} tag
       */
      removeTag(tag) {
         const index = this.tags.findIndex(item => item === tag);
         this.tags.splice(index, this.tags.length - index);

         this.$emit('update:tags', this.tags);
      },

      /**
       * Добавление тэга
       *
       * @param {String|Number} tag
       */
      addTag(tag) {
         this.searchString = '';

         this.tags.push(tag);

         this.$refs.InputRef.focus();

         this.$emit('update:tags', this.tags);
      },

      /**
       * Проверка на Mercedes
       */
      checkMercedes() {
         if (!this.tags.length) {
            return false;
         }

         if (typeof this.tags[0] !== 'string') {
            return false;
         }

         return this.tags[0].toLowerCase() === 'Mercedes-Benz'.toLowerCase();
      },

      /**
       * Метод-флаг максимальной длины тэгов для скрытия листа
       *
       * @return {boolean}
       */
      isMaxLength() {
         let maxCount = 4;

         if (this.checkMercedes()) {
            maxCount += 1;
         }

         return this.tags.length >= maxCount;
      },

      /**
       * Получить текущий активный лист в зависимости от количества тэгов
       *
       * @return {null|string[]|number[]}
       */
      getCurrentList() {
         switch (this.tags.length) {
            case 0:
               return this.marks;
            case 1:
               return this.years;
            case 2:
               return this.body;
            case 3:
               if (this.checkMercedes()) {
                  return this.list;
               }
               return null;
         }
      },

      /**
       * Метод-фильтр в зависимости от значения введенного в поле поиска
       *
       * @return {any[]}
       */
      filterList() {
         const list = this.getCurrentList();

         if (list !== null) {
            return list.filter(item => {
               if (typeof item === 'string') {
                  item = item.trim().toLowerCase();
                  return item.indexOf(this.searchString.toLowerCase()) !== -1;
               }

               if (typeof item === 'number') {
                  return String(item).indexOf(this.searchString) !== -1;
               }

               return false;
            });
         }

         return list;
      },

      /**
       * Регистартор глобального эвента для закрытия листа по клику
       * в любое место кроме компонента
       *
       * @param {PointerEvent} e
       */
      registerGlobalEvent(e) {
         let path = e.path ?? (e.composedPath && e.composedPath());

         let contains = path.filter($el => {
            if ($el.classList) {
               return $el.classList.contains('marks');
            }

            return false;
         });

         if (!contains.length) {
            this.ShowList = false;
         }
      }
   },
   mounted() {
      window.addEventListener('click', this.registerGlobalEvent);
   },
   destroyed() {
      window.removeEventListener('click', this.registerGlobalEvent);
   }
}
</script>

<style lang="sass">

</style>
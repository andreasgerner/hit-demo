<template>
  <div class="wrapper">
    <aside>
      <button class="add" @click="add(0)">Kapitel hinzufügen</button>
      <button class="chapter-nav" v-for="chapter in chapters" @click="scroll(chapter)">Kapitel {{chapter}}</button>
    </aside>
    <draggable
        v-model="list"
        item-key="name"
        class="list"
        ghost-class="ghost"
        handle=".chapter"
        @change="reorder"
    >
      <template #item="{ element }: { element: Block }">
        <div>
          <div v-if="!element.chapter" class="no-chapter">
            {{ element.text }}
          </div>
          <div v-if="element.chapter" class="chapter" :data-id="element.id">
            <div class="grab">
              <svg width="20" height="20" viewBox="0 0 24 24" stroke="black" xmlns="http://www.w3.org/2000/svg">
                <path d="M7 4C7 4.55228 7.44772 5 8 5C8.55228 5 9 4.55228 9 4C9 3.44772 8.55228 3 8 3C7.44772 3 7 3.44772 7 4Z" stroke-width="2"/>
                <path d="M15 4C15 4.55228 15.4477 5 16 5C16.5523 5 17 4.55228 17 4C17 3.44772 16.5523 3 16 3C15.4477 3 15 3.44772 15 4Z" stroke-width="2"/>
                <path d="M7 12C7 12.5523 7.44772 13 8 13C8.55228 13 9 12.5523 9 12C9 11.4477 8.55228 11 8 11C7.44772 11 7 11.4477 7 12Z" stroke-width="2"/>
                <path d="M15 12C15 12.5523 15.4477 13 16 13C16.5523 13 17 12.5523 17 12C17 11.4477 16.5523 11 16 11C15.4477 11 15 11.4477 15 12Z" stroke-width="2"/>
                <path d="M7 20C7 20.5523 7.44772 21 8 21C8.55228 21 9 20.5523 9 20C9 19.4477 8.55228 19 8 19C7.44772 19 7 19.4477 7 20Z" stroke-width="2"/>
                <path d="M15 20C15 20.5523 15.4477 21 16 21C16.5523 21 17 20.5523 17 20C17 19.4477 16.5523 19 16 19C15.4477 19 15 19.4477 15 20Z" stroke-width="2"/>
              </svg>
            </div>
            <span class="chapter-name">{{ element.text }}</span>
            <div class="actions">
              <button @click="addAbove(element)">
                <svg width="16" height="16" viewBox="0 0 1024 1024" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M482 152H542C547.333 152 550 154.667 550 160V864C550 869.333 547.333 872 542 872H482C476.667 872 474 869.333 474 864V160C474 154.667 476.667 152 482 152Z" fill="black"/>
                  <path d="M192 474H864C869.333 474 872 476.667 872 482V542C872 547.333 869.333 550 864 550H160C154.667 550 152 547.333 152 542V482C152 476.667 154.667 474 160 474H192Z" fill="black"/>
                </svg>
                Oberhalb einfügen
              </button>
              <button @click="addBelow(element)">
                <svg width="16" height="16" viewBox="0 0 1024 1024" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M482 152H542C547.333 152 550 154.667 550 160V864C550 869.333 547.333 872 542 872H482C476.667 872 474 869.333 474 864V160C474 154.667 476.667 152 482 152Z" fill="black"/>
                  <path d="M192 474H864C869.333 474 872 476.667 872 482V542C872 547.333 869.333 550 864 550H160C154.667 550 152 547.333 152 542V482C152 476.667 154.667 474 160 474H192Z" fill="black"/>
                </svg>
                Unterhalb einfügen
              </button>
              <button @click="remove(element)">
                <svg width="18" height="18" viewBox="0 0 1024 1024" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M360 184H352C356.4 184 360 180.4 360 176V184ZM360 184H664V176C664 180.4 667.6 184 672 184H664V256H736V176C736 140.7 707.3 112 672 112H352C316.7 112 288 140.7 288 176V256H360V184ZM864 256H160C142.3 256 128 270.3 128 288V320C128 324.4 131.6 328 136 328H196.4L221.1 851C222.7 885.1 250.9 912 285 912H739C773.2 912 801.3 885.2 802.9 851L827.6 328H888C892.4 328 896 324.4 896 320V288C896 270.3 881.7 256 864 256ZM731.3 840H292.7L268.5 328H755.5L731.3 840Z" fill="black"/>
                </svg>
                Löschen
              </button>
            </div>
          </div>
        </div>
      </template>
    </draggable>
  </div>
</template>

<script lang="ts">
import draggable from "vuedraggable";
import { text } from "@/text";

export default {
  name: "simple",
  display: "Simple",
  order: 0,
  components: {
    draggable
  },
  data() {
    return {
      list: loadText(),
      chapters: 0
    }
  },
  methods: {
    add: function (idx: number) {
      this.list.splice(idx > 0 ? idx : 0, 0, {id: ++this.chapters, text: 'Kapitel', chapter: true} as Block);
      this.reorder();
    },
    addAbove: function (elem: Block) {
      const idx = this.list.indexOf(elem) - 1;
      this.add(idx);
    },
    addBelow: function (elem: Block) {
      const idx = this.list.indexOf(elem) + 2;
      this.add(idx);
    },
    remove: function (elem: Block) {
      this.list = this.list.filter((e: Block) => e.id !== elem.id);
      this.chapters--;
      this.reorder();
    },
    reorder: function () {
      let chapterId = 1;
      this.list.forEach((elem: Block) => {
        if (elem.chapter) {
          elem.id = -chapterId;
          elem.text = `Kapitel ${chapterId++}`
        }
      });
    },
    scroll: function (chapter: number) {
      document.querySelector(`[data-id='-${chapter}']`)?.scrollIntoView({
        behavior: "smooth",

      });
    }
  }
};

interface Block {
  id: number;
  text: string;
  chapter: boolean;
}

function loadText() {
  return text.split('.').map((text, id) => {
      return {
        id: id,
        text: text.trim() + '.',
        chapter: false
      } as Block;
    })
}
</script>

<style scoped>
.wrapper {
  display: grid;
  width: 100%;
  min-height: 100svh;
  grid-template-columns: 300px 1fr;
}

.no-chapter {
  margin-left: .25rem;
}

aside {
  background-color: #464646;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  position: sticky;
  align-self: start;
  overflow-y: auto;
  height: 100vh;
  top: 0;
  left: 0;
}

.chapter-nav {
  color: white;
  padding: 1rem 0;
  margin: 0 1rem;
}

.chapter-nav:hover {
  text-decoration: underline;
}

.chapter-nav:not(:last-child) {
  border-bottom: 1px white solid;
}

.list {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  padding: 1rem;
}

.chapter {
  display: flex;
  align-items: center;
  gap: .75rem;
  cursor: pointer;
  scroll-margin-top: 1rem;
  border-left: .25rem solid seagreen;
  background-color: #dadada;
  border-radius: .25rem;
}

.actions {
  display: none;
  align-items: center;
}

.chapter .grab {
  background-color: darkseagreen;
  display: flex;
  align-items: center;
  padding: 0 .25rem;
  height: 3rem;
}

.chapter-name {
  font-size: 1.25rem;
  flex: 1;
}

.actions > button {
  padding: .5rem 1rem;
  font-size: 14px;
  display: flex;
  align-items: center;
  gap: .5rem;
  border-right: 1px solid black;
}

.actions > button:last-child {
  border-right: none;
}

.actions > button:hover {
  text-decoration: underline;
}

.chapter:hover .actions {
  display: flex;
}

.list > div:first-child .chapter {
  padding-top: 0;
}

.list > div:last-child .chapter {
  padding-bottom: 0;
}

.ghost {
  opacity: 0;
}

.add {
  background-color: white;
  width: 100%;
  padding: .5rem .75rem;
  border-radius: .25rem;
  margin-bottom: 1rem;
}

.add:hover {
  background-color: #eeeeee;
}
</style>

<template>
    <div>
        <div class="display">
            
            <Segment ref="segment1" :value="memory[0x83F8]"/>
            <Segment ref="segment2" :value="memory[0x83F9]"/>
            <Segment ref="segment3" :value="memory[0x83FA]"/>
            <Segment ref="segment4" :value="memory[0x83FB]"/>

        </div>
        <section class="controls">
            <fmt-input label="Address:" class="address" :format="16" v-model="start"></fmt-input>
        </section>
        <hr>
        <section class="memory-unit">
            <fmt-input :key="index"
                       :title="pc === index ? `Program Counter Set On ${address(index)}` : address(index)"
                       v-model="memory[index]"
                       :format="format"
                       :readonly="readonly"
                       :bits="8"
                       :zero="true"
                       :active="pc === index"
                       v-for="index in indexes">
            </fmt-input>
        </section>
    </div>
</template>


<script lang="ts">
    import {Component,Prop, Vue} from "vue-property-decorator"
    import {Format, ifmt} from "@/libs/strings"
    import {range2} from "@/libs/extra"


    import FmtInput from "./FmtInput.vue"
    import Segment from "./DispSeg.vue"

    const defaultSize = 8
    @Component({
        components: {FmtInput,Segment},
    })
    export default class Display extends Vue {
        format: Format = Format.Hex

        @Prop()
        readonly!: boolean

        @Prop()
        public memory!: Array<number>

        @Prop()
        public pc!: number

        start: number = 0
        

        get indexes() {
            const start = Number(this.start)
            return range2(start, defaultSize)
        }
        address(index: number){
            return ifmt(index,Format.Hex,4)
        }


        show() {
            (this.$refs.segment1 as Segment).show();
            (this.$refs.segment2 as Segment).show();
            (this.$refs.segment3 as Segment).show();
            (this.$refs.segment4 as Segment).show();

        }
        updated() {
            this.show();
        }
        
    }
</script>

<style scoped>

    .display {
        width: fit-content;
        border: 15px solid #e3edf7;
        padding: 50px 80px;
        border-radius: 40px;
        background: linear-gradient(to right, #c9d3c3, #b5c3ab);
        box-shadow: inset 0 0 10px rgba(0, 0, 0, .2),
            -4px -4px 10px rgba(255, 255, 255, .5),
            8px 8px 15px rgba(0, 0, 0, .1);
    }
    .memory-unit {
        display: grid;
        grid-template-columns: auto auto auto auto auto auto auto auto;
    }

    .memory-unit label {
        margin-bottom: .25em;
        margin-right: .25em;
        width: initial;
    }

    .memory-unit label:hover {
        background-color: #ef8f15;
        box-shadow: 0 0 3px rgb(85, 85, 85);
    }
</style>./DispSeg.vue
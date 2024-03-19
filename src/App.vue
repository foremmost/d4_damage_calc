

<script>

export default {
  //name: 'G4 Damage Calculator',
  components: {

  },
  data(){
    return {
      //Chances  процентные вероятности появления урона
      criticalChance: 8.5,
      vulnChance: 0,
      overpowerDamageChance: 3,
      // Базовые значения урона
      damageFromWeapon: 4057, // Базовый урон от оружия
      attackSpeed: 1.2,

      baseCriticalDamage: 50,
      vulnDamage: 20,

      overpowerDamage: 10,
      baseCharValue: 585,

      sorcSkills: [],
     // allDamage: 32.2,
    //  highHp: 105.7,
      affixes:{
        cold: 32.2, highHp: 105.7
      },
      multipliers:{
        fragile:12
      }
    }
  },
  methods:{
    calcRawAttackPower(){
      return Math.ceil(
          this.damageFromWeapon + (this.damageFromWeapon * this.baseChar )
      );
    },
    calcAttackPower(){
      const _ = this;
      return _.calcRawAttackPower();// + (_.calcRawAttackPower();
    },
    calcAffixes(base){
      const _ = this;
      let percent = 0;
      for(let prop in _.affixes){
        percent+= _.affixes[prop];
      }
      return base*=percent/100;
    },
    calcMultipliers(base){
      const _ = this;
      let newBase = base;
      for(let prop in _.multipliers){
        newBase*= (_.multipliers[prop] / 100);
      }
      return base+newBase;
    },
    calcDamage(percent){
      const _ = this;
      let base = _.calcAttackPower() * (percent/100) * this.baseChar;
      console.log(_.calcAffixes(base))
      //* (_.baseChar/100)
      return Math.round(   _.calcMultipliers(_.calcAffixes(base)) * _.attackSpeed)
    },
    calcCritDamage(percent){
      const _ = this;
      let baseCrit = _.baseCriticalDamage / 100;
      return Math.round(_.calcDamage(percent) + (_.calcDamage(percent) * baseCrit))
    },
    calcVulnDamage(percent){
      const _ = this;
      let baseVuln = _.vulnDamage / 100;
      return Math.round(_.calcDamage(percent) + (_.calcDamage(percent) * (baseVuln)));
    },
    calcCritVulnDamage(percent){
      const _ = this;
      return Math.round(_.calcCritDamage(percent) + _.calcVulnDamage(percent));
    }
  },
  created() {
    const _ = this;
    _.sorcSkills.push( {
      skillName: 'arcLash',
      percent: 63,
    },{
      skillName: 'Ice shard',
      percent: 35
    });
  },
  computed:{
    baseChar(){
      const _ = this;
      return (_.baseCharValue / 10) / 100
    }
  }
}
</script>
<template>
  <h1>D4 Damage calculator</h1>
  <h2>Base damage</h2>
  <ul>
    <li v-for="(sorcSkill,index) in sorcSkills" :key="index">
      <h4>{{ sorcSkill['skillName'] }}:</h4>
      <p>Clear Damage: {{ calcDamage(sorcSkill['percent']) }}</p>
      <p style="color:forestgreen">Vulnerable Damage: {{ calcVulnDamage(sorcSkill['percent']) }}</p>
      <p style="color:tomato">Critical Damage: {{ calcCritDamage(sorcSkill['percent']) }}</p>
      <p style="color:tomato">Critical with Vulnerable Damage: {{ calcCritVulnDamage(sorcSkill['percent']) }}</p>

    </li>
  </ul>
</template>


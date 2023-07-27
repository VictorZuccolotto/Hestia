# Feito por [@VictorZuccolotto](https://github.com/VictorZuccolotto) <!-- omit in toc -->

## A fazeres
- [ ] Pagina Tv - Sources
- [ ] Pagina Timeconfig - Botão Salvar
- [x] Reutilizar paginas de LuzRGB, Luz Dimmer, Ar condicionado para multiplas entidades
- [x] Pagina Luz Dimmer - porcentagem de brilho
- [ ] Pagina cortinas

## --
- [Painel base](#painel-base)
- [Luzes](#Luzes)
    - [Adicionar luzes](#adicionar-luzes)
    - [Adicionar luz RGB](#adicionar-luz-RGB)
    - [Adicionar mais de uma luz RGB](#adicionar-mais-de-uma-luz-RGB)
    - [Adicionar luz dimmer](#adicionar-luz-Dimmer)
    - [Adicionar mais de uma luz dimmer](#adicionar-mais-de-uma-luz-Dimmer)
- [Dispositivos](#Dispositivos)
    - [Ar Condicionado](#ar-condicionado)
        - [Adicionar Ar Condicionado](#adicionar-ar-condicionado)
        - [Adicionar mais de um Ar Condicionado](#adicionar-mais-de-um-ar-condicionado)
    - [Adicionar Receiver](#adicionar-receiver)
    - [Adicionar Spotify](#adicionar-spotify)
    - [Adicionar Tv](#adicionar-tv)
    - [Cortinas](#cortinas)
        - [Adicionar Cortina](#adicionar-cortina)
        - [Adicionar mais de uma Cortina](#adicionar-mais-de-uma-cortina)
- [Cenas](#cenas)

## Painel base

```yaml

```
## Luzes

### Adicionar luzes
binary_sensor:

\#\#\#\#\#\#\#\#PAGINA Luzes\#\#\#\#\#\#\#\#\#\#
```yaml
  - platform: nextion
    name: $device_name Button 5
    page_id: $pag_luzes
    component_id: 12
    internal: true
    on_release:
      then:
        - homeassistant.service:
            service: homeassistant.toggle
            data:
              entity_id: $luz5
```
text_sensor:
```yaml
 ########TxTSensor luzMaster##########
  - platform: homeassistant
    id: luzMaster
    entity_id: $luz5
    filters:
    - map:
      - on -> 1
      - off -> 0
    on_value:
      then:
        - lambda: !lambda |-
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzes}").c_str())){
                id(disp1).send_command_printf("b5.picc=%i", atoi(x.c_str())); //b1 luz1 b2 luz2 b5 luz5
                id(disp1).send_command_printf("b5.picc2=%i",atoi(x.c_str()));
            }   
```
script:

if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzes}").c_str())){
```yaml
            id(disp1).send_command_printf("b5.picc=%i", atoi(id(luzMaster).state.c_str()));
            id(disp1).send_command_printf("b5.picc2=%i",atoi(id(luzMaster).state.c_str()));
```
### Adicionar luz RGB
binary_sensor:

\#\#\#\#\#\#\#\#PAGINA Luzes\#\#\#\#\#\#\#\#\#\#
```yaml
  - platform: nextion
    name: $device_name Button 2
    page_id: $pag_luzes
    component_id: 6
    internal: true
    on_release:
      then:
        - homeassistant.service:
            service: homeassistant.toggle
            data:
              entity_id: $luz2

  - platform: nextion
    name: $device_name Button info 2
    page_id: $pag_luzes
    component_id: 14
    internal: true
    on_release:
      then:
        - lambda: |-
            id(disp1).send_command_printf("LuzRGB.current.val=2"); //Numero da luz
            id(current_rgb).update();
            id(disp1).send_command_printf("page LuzRGB");
```
sensor:
```yaml
 ########Sensor Luz2##########
  - platform: homeassistant
    id: luz2_current_brightness
    entity_id: $luz2 
    attribute: brightness
    on_value:
      then:
        - lambda: |-
            if(id(slider_aux) == false){
              if (!strcmp(id(current_page2).c_str(),to_string("${pag_luzrgb}").c_str())){
                if ((int)(id(current_rgb).state) == 2) { //Numero da luz
                  id(disp1).send_command_printf("brightnessVal.val=%i", (int)x);
                }
              }
            }
        - delay: 100ms
        - lambda: 'id(slider_aux) = false;'
 

  - platform: homeassistant
    id: luz2_current_color_temp
    entity_id: $luz2
    attribute: color_temp
    on_value:
      then:
        - lambda: |-
            if(id(slider_aux) == false){
              if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzrgb}").c_str())){
                if ((int)(id(current_rgb).state) == 2) { //Numero da luz
                  id(disp1).send_command_printf("tempSlider.val=%i", (int)x);
                }
              }
            }
        - delay: 200ms
        - lambda: 'id(slider_aux) = false;'
```
text_sensor:
```yaml
 ########TxTSensor luz2##########
  - platform: homeassistant
    id: luz2
    entity_id: $luz2
    filters:
    - map:
      - on -> 1
      - off -> 0
    on_value:
      then:
        - lambda: !lambda |-
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzrgb}").c_str())){ 
              if((int)(id(current_rgb).state) == 2){ //Numero da luz
                id(disp1).send_command_printf("b0.picc=%i",8 + atoi(x.c_str()));
                id(disp1).send_command_printf("b0.picc2=%i",8 + atoi(x.c_str()));
              }
            }
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzes}").c_str())){
                id(disp1).send_command_printf("b2.picc=%i", atoi(x.c_str())); //b1 luz1 b2 luz2
                id(disp1).send_command_printf("b2.picc2=%i",atoi(x.c_str()));
                id(disp1).send_command_printf("b2info.picc=%i", atoi(x.c_str())); //b1 luz1 b2 luz2
                id(disp1).send_command_printf("b2info.picc2=%i",atoi(x.c_str()));
            }

  - platform: homeassistant
    id: luz2_current_brightness_rgb
    entity_id: $luz2
    attribute: rgb_color
    filters:
    - substitute:
      - "( -> "
      - ") -> "
    - lambda: |-
        char delim[] = ", ";
        char *ptr = strtok((char*)x.c_str(), delim);
        char rgb[3][4];
        int i=0;
        while(ptr != NULL){
          strcpy(rgb[i],ptr);
          i++;
          ptr = strtok(NULL, delim);
        }
        int r = atoi(rgb[0]);
        int g = atoi(rgb[1]);
        int b = atoi(rgb[2]);
        int nextion = (r/8)*2048 + (g/4)*32 + (b/8);
        char str[6];
        sprintf(str, "%d", nextion);
        return {str};

  - platform: homeassistant
    id: luz2_current_brightness_rgb_slider
    entity_id: $luz2
    attribute: rgb_color
    filters:
    - substitute:
      - "( -> "
      - ") -> "
    - lambda: |-
        char delim[] = ", ";
        char *ptr = strtok((char*)x.c_str(), delim);
        char rgb[3][4];
        int i=0;
        while(ptr != NULL){
          strcpy(rgb[i],ptr);
          i++;
          ptr = strtok(NULL, delim);
        }
        int r = atoi(rgb[0]);
        int g = atoi(rgb[1]);
        int b = atoi(rgb[2]);

        char str[6];
    
        if(r < g && r < b){
          if(g >= b){
            sprintf(str, "%d", b+510);
          }else{
            sprintf(str, "%d", 1020-g);
          }
        }else if(g < b){
          if(b >= r){
            sprintf(str, "%d", 1020+r);
          }else{
            sprintf(str, "%d", 1529-b);
          }
        }else{
          if(r >= g){
            sprintf(str, "%d", g);
          }else{
            sprintf(str, "%d", 510-r);
          }
        }
        return {str};  
    on_value:
      then:
        - lambda: |-
            if(id(slider_aux) == false){
              if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzrgb}").c_str())){ 
                if((int)(id(current_rgb).state) == 2){ //Numero da luz
                  id(disp1).send_command_printf("colorSlider.val=%s", x.c_str());
                }
              }
            }
        - delay: 200ms
        - lambda: 'id(slider_aux) = false;'
```
script:

if (!strcmp(id(current_page2).c_str(),to_string("${pag_luzrgb}").c_str()))\{// RGB page
```yaml
            if((int)(id(current_rgb).state) == 1){ //Numero da luz
              //Outra luz rgb
            }else if((int)(id(current_rgb).state) == 2){ //Numero da luz
              id(disp1).set_component_text_printf("nome", "%s", "Corredor");
              id(disp1).send_command_printf("brightnessVal.val=%i", (int)id(luz2_current_brightness).state); 
              id(disp1).send_command_printf("tempSlider.val=%i", (int)id(luz2_current_color_temp).state);
              id(disp1).send_command_printf("colorSlider.val=%s", !strcmp(id(luz2_current_brightness_rgb_slider).state.c_str(),to_string("").c_str()) ? "249" : id(luz2_current_brightness_rgb_slider).state.c_str());
              id(disp1).send_command_printf("b0.picc=%i", 8 + atoi(id(luz2).state.c_str()));
              id(disp1).send_command_printf("b0.picc2=%i",8 + atoi(id(luz2).state.c_str()));
            }else if((int)(id(current_rgb).state) == 3){ //Numero da luz
                //Outra luz rgb
            }else if((int)(id(current_rgb).state) == 4){ //Numero da luz
                //Outra luz rgb
            }
```

if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzes}").c_str())){
```yaml
            id(disp1).send_command_printf("b2.picc=%i", atoi(id(luz2).state.c_str()));
            id(disp1).send_command_printf("b2.picc2=%i",atoi(id(luz2).state.c_str()));
            id(disp1).send_command_printf("b2info.picc=%i", atoi(id(luz2).state.c_str()));
            id(disp1).send_command_printf("b2info.picc2=%i",atoi(id(luz2).state.c_str()));
```

### Adicionar luz Dimmer
binary_sensor:

\#\#\#\#\#\#\#\#PAGINA Luzes\#\#\#\#\#\#\#\#\#\#
```yaml
  - platform: nextion
    name: $device_name Button 4
    page_id: $pag_luzes
    component_id: 15
    internal: true
    on_release:
      then:
        - homeassistant.service:
            service: homeassistant.toggle
            data:
              entity_id: $luz4

  - platform: nextion
    name: $device_name Button info 4
    page_id: $pag_luzes
    component_id: 16
    internal: true
    on_release:
      then:
        - lambda: |-
            id(disp1).send_command_printf("LuzDimmer.current.val=4");
            id(current_dimmer).update();
            id(disp1).send_command_printf("page LuzDimmer");
```
sensor:
```yaml
 ########Sensor Luz4##########
  - platform: homeassistant
    id: luz4_current_brightness
    entity_id: $luz4 
    attribute: brightness
    on_value:
      then:
        - lambda: |-
            if(id(slider_aux) == false){
              if (!strcmp(id(current_page2).c_str(),to_string("${pag_luzdimmer}").c_str())){
                if ((int)(id(current_dimmer).state) == 4) {
                  id(disp1).send_command_printf("brightnessVal2.val=%i", (int)x);
                  id(disp1).set_component_text_printf("t0", "%i%%", (int)((x/255)*100));
                }
              }
            }
        - delay: 100ms
        - lambda: 'id(slider_aux) = false;'
```
text_sensor:
```yaml
 ########TxTSensor luz4##########
  - platform: homeassistant
    id: luz4
    entity_id: $luz4
    filters:
    - map:
      - on -> 1
      - off -> 0
    on_value:
      then:
        - lambda: !lambda |-
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzdimmer}").c_str())){
              if((int)(id(current_dimmer).state) == 4){ 
                id(disp1).send_command_printf("b0.picc=%i",23 + atoi(x.c_str()));
                id(disp1).send_command_printf("b0.picc2=%i",23 + atoi(x.c_str()));
              }
            }
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzes}").c_str())){
                id(disp1).send_command_printf("b4.picc=%i", atoi(x.c_str()));
                id(disp1).send_command_printf("b4.picc2=%i",atoi(x.c_str()));
            } 
```
script:

if (!strcmp(id(current_page2).c_str(),to_string("${pag_luzdimmer}").c_str())){//
```yaml
            if((int)(id(current_dimmer).state) == 1){ 
              
            }else if((int)(id(current_dimmer).state) == 2){

            }else if((int)(id(current_dimmer).state) == 3){

            }else if((int)(id(current_dimmer).state) == 4){
              id(disp1).set_component_text_printf("nome", "%s", "Sala");
              id(disp1).send_command_printf("brightnessVal2.val=%i", (int)id(luz4_current_brightness).state);
              id(disp1).set_component_text_printf("t0", "%i%%", (int)((id(luz4_current_brightness).state/255)*100));
              id(disp1).send_command_printf("b0.picc=%i", 23 + atoi(id(luz4).state.c_str()));
              id(disp1).send_command_printf("b0.picc2=%i",23 + atoi(id(luz4).state.c_str()));
            }
```

if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzes}").c_str())){
```yaml
            id(disp1).send_command_printf("b4.picc=%i", atoi(id(luz4).state.c_str()));
            id(disp1).send_command_printf("b4.picc2=%i",atoi(id(luz4).state.c_str()));
```

## Dispositivos

### Ar Condicionado

#### Adicionar ar condicionado
binary_sensor:


\#\#\#\#\#\#\#\#PAGINA Dispositivos\#\#\#\#\#\#\#\#\#\#
```yaml
  - platform: nextion
    name: $device_name dispositivos Button 1
    page_id: $pag_dispositivos
    component_id: 3
    internal: true
    on_release:
      then:
        - rtttl.play: $barulho
        - if:
            condition:
              lambda: 'return id(ar1_current_mode).state == "0";'
            then:
              - homeassistant.service:
                  service: climate.turn_on
                  data_template:
                    entity_id: $ar1
            else:
              - homeassistant.service:
                  service: climate.turn_off
                  data_template:
                    entity_id: $ar1

  - platform: nextion
    name: $device_name dispositivos Button info 1
    page_id: $pag_dispositivos
    component_id: 13
    internal: true
    on_release:
      then:
        - lambda: |-
            id(disp1).send_command_printf("Ar.current.val=1");
            id(current_ar).update();
            id(disp1).send_command_printf("page Ar");
```

\#\#\#\#\#\#\#\#PAGINA Ar\#\#\#\#\#\#\#\#\#\#

atenção aqui
```yaml
  - platform: nextion
    name: $device_name Minus Button
    page_id: $pag_ar
    component_id: 1
    internal: true
    on_release:
      then:
        - rtttl.play: $barulho
        - homeassistant.service:
            service: climate.set_temperature
            data_template:
              entity_id: !lambda |-
                if ((int)(id(current_ar).state) == 1) {
                  return "${ar1}";
                }else { //else if ((int)(id(current_ar).state) == Numero de outro ar) { no caso 2
                  return "${ar2}";
                }
              temperature: !lambda |-
                if ((int)(id(current_ar).state) == 1) {
                  return "{{ (state_attr(\"$ar1\", \"temperature\")|round(0) == (state_attr(\"$ar1\", \"min_temp\"))) |iif((state_attr(\"$ar1\", \"temperature\")|round(0)), (state_attr(\"$ar1\", \"temperature\")|round(0)) - 1) }}";
                }else { //else if ((int)(id(current_ar).state) == Numero de outro ar) { no caso 2
                  return "{{ (state_attr(\"$ar2\", \"temperature\")|round(0) == (state_attr(\"$ar2\", \"min_temp\"))) |iif((state_attr(\"$ar2\", \"temperature\")|round(0)), (state_attr(\"$ar2\", \"temperature\")|round(0)) - 1) }}";
                }
    
  - platform: nextion
    name: $device_name Plus Button
    page_id: $pag_ar
    component_id: 2
    internal: true
    on_release:
      then:
        - rtttl.play: $barulho
        - homeassistant.service:
            service: climate.set_temperature
            data_template:
              entity_id: !lambda |-
                if ((int)(id(current_ar).state) == 1) {
                  return "${ar1}";
                }else {
                  return "${ar2}";
                }
              temperature: !lambda |-
                if ((int)(id(current_ar).state) == 1) {
                  return "{{ (state_attr(\"$ar1\", \"temperature\")|round(0) == (state_attr(\"$ar1\", \"max_temp\"))) |iif((state_attr(\"$ar1\", \"temperature\")|round(0)), (state_attr(\"$ar1\", \"temperature\")|round(0)) + 1) }}";
                }else {
                  return "{{ (state_attr(\"$ar2\", \"temperature\")|round(0) == (state_attr(\"$ar2\", \"max_temp\"))) |iif((state_attr(\"$ar1\", \"temperature\")|round(0)), (state_attr(\"$ar2\", \"temperature\")|round(0)) + 1) }}";
                }
    
  - platform: nextion
    name: $device_name Fan Speed Button
    page_id: $pag_ar
    component_id: 3
    internal: true
    on_release:
      then:
        - rtttl.play: $barulho
        - homeassistant.service:
            service: climate.set_fan_mode
            data:
              entity_id: !lambda |-
                if ((int)(id(current_ar).state) == 1) {
                  return "${ar1}";
                }else {
                  return "${ar2}";
                }
              fan_mode: !lambda |-
                if ((int)(id(current_ar).state) == 1) {
                  if (id(ar1_fan_speed).state == "1") {
                    id(ar1_fan_speed).state = "2";
                    return "medium";
                  } else if (id(ar1_fan_speed).state == "2") {
                    id(ar1_fan_speed).state = "3";
                    return "high";
                  } else if (id(ar1_fan_speed).state == "3") {
                    id(ar1_fan_speed).state = "4";
                    return "auto";
                  } else if (id(ar1_fan_speed).state == "4") {
                    id(ar1_fan_speed).state = "1";
                    return "low";
                  } else {
                    id(ar1_fan_speed).state = "4";
                    return "auto";
                  }
                } else{
                  if (id(ar2_fan_speed).state == "1") {
                    id(ar2_fan_speed).state = "2";
                    return "medium";
                  } else if (id(ar2_fan_speed).state == "2") {
                    id(ar2_fan_speed).state = "3";
                    return "high";
                  } else if (id(ar2_fan_speed).state == "3") {
                    id(ar2_fan_speed).state = "4";
                    return "auto";
                  } else if (id(ar2_fan_speed).state == "4") {
                    id(ar2_fan_speed).state = "1";
                    return "low";
                  } else {
                    id(ar2_fan_speed).state = "4";
                    return "auto";
                  }
                }
    
  - platform: nextion
    name: $device_name Power Off Button
    page_id: $pag_ar
    component_id: 4
    internal: true
    on_release:
      then:
        - rtttl.play: $barulho
        - if:
            condition:
              lambda: !lambda |-
                if ((int)(id(current_ar).state) == 1) {
                  return id(ar1_current_mode).state == "0";
                }else{
                  return id(ar2_current_mode).state == "0";
                }
            then:
              - homeassistant.service:
                  service: climate.turn_on
                  data_template:
                    entity_id: !lambda |-
                      if ((int)(id(current_ar).state) == 1) {
                        return "${ar1}";
                      }else {
                        return "${ar2}";
                      }
            else:
              - homeassistant.service:
                  service: climate.turn_off
                  data_template:
                    entity_id: !lambda |-
                      if ((int)(id(current_ar).state) == 1) {
                        return "${ar1}";
                      }else {
                        return "${ar2}";
                      }
   
  - platform: nextion
    name: $device_name Auto Button
    page_id: $pag_ar
    component_id: 5
    internal: true
    on_release:
      then:
        - if:
            condition:
              lambda: !lambda |-
                if ((int)(id(current_ar).state) == 1) {
                  return id(ar1_current_mode).state == "auto";
                }else{
                  return id(ar2_current_mode).state == "auto";
                }
            then:
              - rtttl.play: $barulho
              - homeassistant.service:
                  service: climate.set_hvac_mode
                  data:
                    entity_id: !lambda |-
                      if ((int)(id(current_ar).state) == 1) {
                        return "${ar1}";
                      }else {
                        return "${ar2}";
                      }
                    hvac_mode: 'off'
            else:
              - rtttl.play: $barulho
              - homeassistant.service:
                  service: climate.set_hvac_mode
                  data:
                    entity_id: !lambda |-
                      if ((int)(id(current_ar).state) == 1) {
                        return "${ar1}";
                      }else {
                        return "${ar2}";
                      }
                    hvac_mode: heat_cool
    
  - platform: nextion
    name: $device_name Cool Button
    page_id: $pag_ar
    component_id: 6
    internal: true
    on_release:
      then:
        - if:
            condition:
              lambda: !lambda |-
                if ((int)(id(current_ar).state) == 1) {
                  return id(ar1_current_mode).state == "cool";
                }else{
                  return id(ar2_current_mode).state == "cool";
                }
            then:
              - rtttl.play: $barulho
              - homeassistant.service:
                  service: climate.set_hvac_mode
                  data:
                    entity_id: !lambda |-
                      if ((int)(id(current_ar).state) == 1) {
                        return "${ar1}";
                      }else {
                        return "${ar2}";
                      }
                    hvac_mode: 'off'
            else:
              - rtttl.play: $barulho
              - homeassistant.service:
                  service: climate.set_hvac_mode
                  data:
                    entity_id: !lambda |-
                      if ((int)(id(current_ar).state) == 1) {
                        return "${ar1}";
                      }else {
                        return "${ar2}";
                      }
                    hvac_mode: cool
    
  - platform: nextion
    name: $device_name Humidity  Button
    page_id: $pag_ar
    component_id: 7
    internal: true
    on_release:
      then:
        - if:
            condition:
              lambda: !lambda |-
                if ((int)(id(current_ar).state) == 1) {
                  return id(ar1_current_mode).state == "humidity";
                }else{
                  return id(ar2_current_mode).state == "humidity";
                }
            then:
              - rtttl.play: $barulho
              - homeassistant.service:
                  service: climate.set_hvac_mode
                  data:
                    entity_id: !lambda |-
                      if ((int)(id(current_ar).state) == 1) {
                        return "${ar1}";
                      }else {
                        return "${ar2}";
                      }
                    hvac_mode: 'off'
            else:
              - rtttl.play: $barulho
              - homeassistant.service:
                  service: climate.set_hvac_mode
                  data:
                    entity_id: !lambda |-
                      if ((int)(id(current_ar).state) == 1) {
                        return "${ar1}";
                      }else {
                        return "${ar2}";
                      }
                    hvac_mode: dry
    
  - platform: nextion
    name: $device_name Fan Button
    page_id: $pag_ar
    component_id: 8
    internal: true
    on_release:
      then:
        - if:
            condition:
              lambda: !lambda |-
                if ((int)(id(current_ar).state) == 1) {
                  return id(ar1_current_mode).state == "fan";
                }else{
                  return id(ar2_current_mode).state == "fan";
                }
            then:
              - rtttl.play: $barulho
              - homeassistant.service:
                  service: climate.set_hvac_mode
                  data:
                    entity_id: !lambda |-
                      if ((int)(id(current_ar).state) == 1) {
                        return "${ar1}";
                      }else {
                        return "${ar2}";
                      }
                    hvac_mode: 'off'
            else:
              - rtttl.play: $barulho
              - homeassistant.service:
                  service: climate.set_hvac_mode
                  data:
                    entity_id: !lambda |-
                      if ((int)(id(current_ar).state) == 1) {
                        return "${ar1}";
                      }else {
                        return "${ar2}";
                      }
                    hvac_mode: fan_only
```

sensor:
```yaml
 ########Sensor ar1##########
  - platform: homeassistant
    id: ar1_temperature
    entity_id: $ar1
    attribute: temperature
    on_value:
      then:
        - lambda: |-
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_ar}").c_str())){
              if((int)(id(current_ar).state) == 1){ //numero do ar
                if(id(ar1_current_mode).state == "0"){
                  id(disp1).set_component_text_printf("targetTemp", "--");
                  id(disp1).send_command_printf("vis t0,0");
                }else{
                  id(disp1).set_component_text_printf("targetTemp", "%i""°", (int)x);
                  id(disp1).send_command_printf("vis t0,1");
                }
              }
            }

  - platform: homeassistant
    id: ar1_current_temperature
    entity_id: $ar1
    attribute: current_temperature
    on_value:
      then:
        - lambda: |-
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_ar}").c_str())){
              if((int)(id(current_ar).state) == 1){ //numero do ar
                id(disp1).set_component_text_printf("currentTemp", "%i""°", (int)x);
              }
            }
```
text_sensor:
```yaml
 ########TxTSensor ar1##########
  - platform: homeassistant
    id: ar1_current_mode
    entity_id: $ar1
    filters:
      - map:
        - off -> 0
        - heat_cool -> auto #autoActive
        - cool -> cool #coolActive
        - dry -> humidity #dryActive
        - fan_only -> fan #fanActive
    on_value:
      then:
        - lambda: |-
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_ar}").c_str())){
              if((int)(id(current_ar).state) == 1){ //numero do ar
                id(disp1).send_command_printf("auto.picc=11");
                id(disp1).send_command_printf("auto.picc2=11");
                id(disp1).send_command_printf("cool.picc=11");
                id(disp1).send_command_printf("cool.picc2=11");
                id(disp1).send_command_printf("humidity.picc=11");
                id(disp1).send_command_printf("humidity.picc2=11");
                id(disp1).send_command_printf("fan.picc=11");
                id(disp1).send_command_printf("fan.picc2=11");
                if(x=="0"){
                  id(disp1).send_command_printf("vis fanSpeed,0");
                  id(disp1).send_command_printf("vis t0,0");
                  id(disp1).set_component_text_printf("targetTemp", "--");
                  id(disp1).send_command_printf("power.picc=11");
                  id(disp1).send_command_printf("power.picc2=11");
                }else{
                  id(disp1).send_command_printf("vis fanSpeed,1");
                  id(disp1).send_command_printf("vis t0,1");
                  id(disp1).set_component_text_printf("targetTemp", "%i""°", (int)id(ar1_temperature).state);
                  id(disp1).send_command_printf("%s.picc=12",x.c_str());
                  id(disp1).send_command_printf("%s.picc2=12",x.c_str());
                  id(disp1).send_command_printf("power.picc=12");
                  id(disp1).send_command_printf("power.picc2=12");
                }
              }
            }
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_dispositivos}").c_str())){
              if(x=="0"){
                id(disp1).send_command_printf("b0.picc=4");
                id(disp1).send_command_printf("b0.picc2=4");
                id(disp1).send_command_printf("b0info.picc=4");
                id(disp1).send_command_printf("b0info.picc2=4");
              }else{
                id(disp1).send_command_printf("b0.picc=5");
                id(disp1).send_command_printf("b0.picc2=5");
                id(disp1).send_command_printf("b0info.picc=5");
                id(disp1).send_command_printf("b0info.picc2=5");
              }
            }


  - platform: homeassistant
    id: ar1_fan_speed
    entity_id: $ar1
    attribute: fan_mode
    filters:
      - map:
        - low -> 1
        - medium -> 2
        - high -> 3
        - auto -> 4
    on_value:
      then:
        - lambda: !lambda |-
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_ar}").c_str())){
              if((int)(id(current_ar).state) == 1){ //numero do ar
                if (x == "1") {
                  id(disp1).send_command_printf("fanSpeed.pic=14");
                  id(disp1).send_command_printf("fanSpeed.pic2=14");
                } else if (x == "2") {
                  id(disp1).send_command_printf("fanSpeed.pic=15");
                  id(disp1).send_command_printf("fanSpeed.pic2=15");
                } else if (x == "3") {
                  id(disp1).send_command_printf("fanSpeed.pic=16");
                  id(disp1).send_command_printf("fanSpeed.pic2=16");
                } else if (x == "4") {
                  id(disp1).send_command_printf("fanSpeed.pic=13");
                  id(disp1).send_command_printf("fanSpeed.pic2=13");
                }
              }
            }
```
script:

if (!strcmp(id(current_page2).c_str(),to_string("${pag_ar}").c_str())){// Ar page
```yaml
            id(disp1).send_command_printf("auto.picc=11");
            id(disp1).send_command_printf("auto.picc2=11");
            id(disp1).send_command_printf("cool.picc=11");
            id(disp1).send_command_printf("cool.picc2=11");
            id(disp1).send_command_printf("humidity.picc=11");
            id(disp1).send_command_printf("humidity.picc2=11");
            id(disp1).send_command_printf("fan.picc=11");
            id(disp1).send_command_printf("fan.picc2=11");
            id(disp1).send_command_printf("power.picc=11");
            id(disp1).send_command_printf("power.picc2=11");

            if((int)(id(current_ar).state) == 1){ //Numero do ar
              id(disp1).set_component_text_printf("nome", "%s", "Ar Lucas");
              id(disp1).set_component_text_printf("currentTemp", "%i""°", (int)id(ar1_current_temperature).state);
              if (id(ar1_current_mode).state == "0") {
                id(disp1).set_component_text_printf("targetTemp", "--");
              } else {
                id(disp1).send_command_printf("vis fanSpeed,1");
                id(disp1).send_command_printf("vis t0,1");
                id(disp1).set_component_text_printf("targetTemp", "%i""°", (int)id(ar1_temperature).state);
                id(disp1).send_command_printf("power.picc=12");
                id(disp1).send_command_printf("power.picc2=12");
                id(disp1).send_command_printf("%s.picc=12",id(ar1_current_mode).state.c_str());
                id(disp1).send_command_printf("%s.picc2=12",id(ar1_current_mode).state.c_str());
              }
              if (id(ar1_fan_speed).state == "1") {
                id(disp1).send_command_printf("fanSpeed.pic=14");
                id(disp1).send_command_printf("fanSpeed.pic2=14");
              } else if (id(ar1_fan_speed).state == "2") {
                id(disp1).send_command_printf("fanSpeed.pic=15");
                id(disp1).send_command_printf("fanSpeed.pic2=15");
              } else if (id(ar1_fan_speed).state == "3") {
                id(disp1).send_command_printf("fanSpeed.pic=16");
                id(disp1).send_command_printf("fanSpeed.pic2=16");
              } else if (id(ar1_fan_speed).state == "4") {
                id(disp1).send_command_printf("fanSpeed.pic=13");
                id(disp1).send_command_printf("fanSpeed.pic2=13");
              }
            }else{ // else if numero do ar
              id(disp1).set_component_text_printf("nome", "%s", "Ar Teste 2");
              id(disp1).set_component_text_printf("currentTemp", "%i""°", (int)id(ar2_current_temperature).state);
              if (id(ar2_current_mode).state == "0") {
                id(disp1).set_component_text_printf("targetTemp", "--");
              } else {
                id(disp1).send_command_printf("vis fanSpeed,1");
                id(disp1).send_command_printf("vis t0,1");
                id(disp1).set_component_text_printf("targetTemp", "%i""°", (int)id(ar2_temperature).state);
                id(disp1).send_command_printf("power.picc=12");
                id(disp1).send_command_printf("power.picc2=12");
                id(disp1).send_command_printf("%s.picc=12",id(ar2_current_mode).state.c_str());
                id(disp1).send_command_printf("%s.picc2=12",id(ar2_current_mode).state.c_str());
              }
              if (id(ar2_fan_speed).state == "1") {
                id(disp1).send_command_printf("fanSpeed.pic=14");
                id(disp1).send_command_printf("fanSpeed.pic2=14");
              } else if (id(ar2_fan_speed).state == "2") {
                id(disp1).send_command_printf("fanSpeed.pic=15");
                id(disp1).send_command_printf("fanSpeed.pic2=15");
              } else if (id(ar2_fan_speed).state == "3") {
                id(disp1).send_command_printf("fanSpeed.pic=16");
                id(disp1).send_command_printf("fanSpeed.pic2=16");
              } else if (id(ar2_fan_speed).state == "4") {
                id(disp1).send_command_printf("fanSpeed.pic=13");
                id(disp1).send_command_printf("fanSpeed.pic2=13");
              }
            }
          }
```

if (!strcmp(id(current_page2).c_str(),to_string("${pag_dispositivos}").c_str())){//
```yaml
            if(id(ar1_current_mode).state == "0"){ 
              id(disp1).send_command_printf("b0.picc=4");
              id(disp1).send_command_printf("b0.picc2=4");
              id(disp1).send_command_printf("b0info.picc=4");
              id(disp1).send_command_printf("b0info.picc2=4");
            }else{
              id(disp1).send_command_printf("b0.picc=5");
              id(disp1).send_command_printf("b0.picc2=5");
              id(disp1).send_command_printf("b0info.picc=5");
              id(disp1).send_command_printf("b0info.picc2=5");
            }
          }
```

### Adicionar receiver

### Adicionar Spotify

### Adicionar TV

### Cortinas

#### Adicionar cortina

#### Adicionar mais de uma cortina

## Cenas








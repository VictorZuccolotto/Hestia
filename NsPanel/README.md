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
            if((int)(id(current_rgb).state) == 2){ //Numero da luz
              id(disp1).set_component_text_printf("nome", "%s", "Corredor");
              id(disp1).send_command_printf("brightnessVal.val=%i", (int)id(luz2_current_brightness).state);
              id(disp1).send_command_printf("tempSlider.val=%i", (int)id(luz2_current_color_temp).state);
              id(disp1).send_command_printf("colorSlider.val=%s", !strcmp(id(luz2_current_brightness_rgb_slider).state.c_str(),to_string("").c_str()) ? "249" : id(luz2_current_brightness_rgb_slider).state.c_str());
              id(disp1).send_command_printf("b0.picc=%i", 8 + atoi(id(luz2).state.c_str()));
              id(disp1).send_command_printf("b0.picc2=%i",8 + atoi(id(luz2).state.c_str()));
            }
```
### Adicionar mais de uma luz RGB
binary_sensor:


\#\#\#\#\#\#\#\#PAGINA Luzes\#\#\#\#\#\#\#\#\#\#
```yaml
  - platform: nextion
    name: $device_name Button 1
    page_id: $pag_luzes
    component_id: 5
    internal: true
    on_release:
      then:
        - homeassistant.service:
            service: light.toggle
            data_template:
              entity_id: $luz1

  - platform: nextion
    name: $device_name Button info 1
    page_id: $pag_luzes
    component_id: 13
    internal: true
    on_release:
      then:
        - lambda: |-
            id(disp1).send_command_printf("LuzRGB.current.val=1"); //Numero da luz
            id(current_rgb).update();
            id(disp1).send_command_printf("page LuzRGB");
```
sensor:
```yaml
 ########Sensor Luz1##########
  - platform: homeassistant
    id: luz1_current_brightness
    entity_id: $luz1 
    attribute: brightness
    on_value:
      then:
        - lambda: |-
            if(id(slider_aux) == false){
              if (!strcmp(id(current_page2).c_str(),to_string("${pag_luzrgb}").c_str())){
                if ((int)(id(current_rgb).state) == 1) { //Numero da luz
                  id(disp1).send_command_printf("brightnessVal.val=%i", (int)x);
                }
              }
            }
        - delay: 100ms
        - lambda: 'id(slider_aux) = false;'
 

  - platform: homeassistant
    id: luz1_current_color_temp
    entity_id: $luz1
    attribute: color_temp
    on_value:
      then:
        - lambda: |-
            if(id(slider_aux) == false){
              if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzrgb}").c_str())){
                if ((int)(id(current_rgb).state) == 1) { //Numero da luz
                  id(disp1).send_command_printf("tempSlider.val=%i", (int)x);
                }
              }
            }
        - delay: 200ms
        - lambda: 'id(slider_aux) = false;'

```
text_sensor:
```yaml
 ########TxTSensor luz1##########
  - platform: homeassistant
    id: luz1
    entity_id: $luz1
    filters:
    - map:
      - on -> 1
      - off -> 0
    on_value:
      then:
        - lambda: !lambda |-
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzrgb}").c_str())){
              if((int)(id(current_rgb).state) == 1){  //Numero da luz
                id(disp1).send_command_printf("b0.picc=%i",8 + atoi(x.c_str()));
                id(disp1).send_command_printf("b0.picc2=%i",8 + atoi(x.c_str()));
              }
            }
            if(!strcmp(id(current_page2).c_str(),to_string("${pag_luzes}").c_str())){
                id(disp1).send_command_printf("b1.picc=%i", atoi(x.c_str())); //b1 luz1 b2 luz2
                id(disp1).send_command_printf("b1.picc2=%i",atoi(x.c_str()));
                id(disp1).send_command_printf("b1info.picc=%i", atoi(x.c_str())); //b1 luz1 b2 luz2
                id(disp1).send_command_printf("b1info.picc2=%i",atoi(x.c_str()));
            }

  - platform: homeassistant
    id: luz1_current_brightness_rgb
    entity_id: $luz1
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
    id: luz1_current_brightness_rgb_slider
    entity_id: $luz1 
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
                if((int)(id(current_rgb).state) == 1){ // Numero da luz
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
              id(disp1).set_component_text_printf("nome", "%s", "Spot");
              id(disp1).send_command_printf("brightnessVal.val=%i", (int)id(luz1_current_brightness).state);
              id(disp1).send_command_printf("tempSlider.val=%i", (int)id(luz1_current_color_temp).state);
              id(disp1).send_command_printf("colorSlider.val=%s", !strcmp(id(luz1_current_brightness_rgb_slider).state.c_str(),to_string("").c_str()) ? "249" : id(luz1_current_brightness_rgb_slider).state.c_str());
              id(disp1).send_command_printf("b0.picc=%i", 8 + atoi(id(luz1).state.c_str()));
              id(disp1).send_command_printf("b0.picc2=%i",8 + atoi(id(luz1).state.c_str()));
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

### Adicionar luz Dimmer

### Adicionar mais de uma luz Dimmer

## Dispositivos

### Ar Condicionado

#### Adicionar ar condicionado

#### Adicionar mais de um ar condicionado

### Adicionar receiver

### Adicionar Spotify

### Adicionar TV

### Cortinas

#### Adicionar cortina

#### Adicionar mais de uma cortina

## Cenas








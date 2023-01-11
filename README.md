# Онтология по игре [МИР ТАНКОВ](https://tanki.su/ "Ссылка на сайт игры")

[Pykeen example](https://colab.research.google.com/drive/16v9vBSaYuRo9L-vidFUWArGQ_-02cnHt?usp=sharing#scrollTo=qy8rumB1X6L4)

## Компетентностные вопросы к онтологии
1. Сколько игроков в бою превысили свои средние показатели по урону на используемом танке?
2. Какой из танков, представленных в бою, имеется у наибольшего числа игроков в статистике?
3. Какой самый популярный тип техники по сумме сыгранных боёв среди игроков?
4. Тяжелые танки какой нации наименее результативны?
5. Кто повалил больше всего деревьев в среднем за бой?

### 1. Сколько игроков в бою превысили свои средние показатели по урону на используемом танке?
```
player_success_query = """
  PREFIX : <http://www.semanticweb.org/ko4ebhuk/ontologies/2022/10/tanks#>
  PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
  SELECT (COUNT(?player) as ?superiorPlayers) WHERE {
    ?battleResults a :BattleResults .
    ?battleResults :isResultsOfPlayer ?player .
    ?battleResults :playedOn ?tank .
    ?battleResults :batResDamage ?damageInBattle .
    ?playerStatOnTank :tankStatisticOf ?player .
    ?playerStatOnTank :tankStatisticOn ?tank .
    ?playerStatOnTank :concreteTankDamageDealt ?damageDealt .
    ?playerStatOnTank :concreteTankBattles ?battlesOnTank .
    FILTER (xsd:float(?damageInBattle) > ?damageDealt/?battlesOnTank)
  }
"""

for row in tanks_ontology_graph.query(player_success_query):
    print(f"{row.superiorPlayers} of players surpassed themselves")
```
```
Output: 85 of players surpassed themselves
```

### 5. Кто повалил больше всего деревьев в среднем за бой?
```
max_treesCut_query = """
  PREFIX : <http://www.semanticweb.org/ko4ebhuk/ontologies/2022/10/tanks#>
  PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
  SELECT ?name (xsd:float(?treesCut)/xsd:float(?battlesCount) AS ?result) WHERE {
    ?player a :Player .
    ?player :nickName ?name .
    ?player :hasStatistic ?statistic .
    ?statistic :treesCut ?treesCut .
    ?statistic :battles ?battlesCount .
  }
  ORDER BY DESC(?result)
  LIMIT 1
"""

for row in tanks_ontology_graph.query(max_treesCut_query):
    print(f"{row.name} avg_cuts {row.result}")
```
```
Output: 15_MunyT_ADA avg_cuts 3.426887094707164
```
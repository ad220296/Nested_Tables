# 🧩 2.2 Nested Tables in PL/SQL

## 🔍 Einführung

**Nested Tables** sind flexible Datenstrukturen in PL/SQL. Sie erlauben es, mehrere Werte vom gleichen Typ zu speichern – vergleichbar mit Arrays oder Listen in anderen Sprachen.

> ✨ Du kannst Nested Tables verwenden
> - **als Array** mit Ganzzahlen als Index (klassische Reihenfolge)
> - **als Hashtabelle** mit benutzerdefinierten Schüsseln (`INDEX BY`)
> - für **Massenoperationen** wie `BULK COLLECT`

---

## 📘 1. Nested Table als Array (ohne INDEX BY)
```sql
DECLARE
    TYPE name_table_t IS TABLE OF VARCHAR2(32);
    name_table name_table_t;
BEGIN
    name_table := name_table_t();
    name_table.EXTEND;
    name_table(1) := 'Gerhard';

    name_table.EXTEND;
    name_table(name_table.LAST) := 'Clemens';

    FOR i IN name_table.FIRST .. name_table.LAST LOOP
        dbms_output.put_line(name_table(i));
    END LOOP;
END;
/
```
📌 Typische Array-Nutzung: `EXTEND`, `FIRST`, `LAST`, `COUNT`, Index ab 1

---

## 🔢 2. Nested Table als Hashtabelle (mit `INDEX BY`)
```sql
DECLARE
    TYPE name_sal_table_t IS TABLE OF NUMBER INDEX BY VARCHAR2(32);
    name_sal_table name_sal_table_t;
BEGIN
    name_sal_table('Gerhard') := 2000;
    name_sal_table('Anton') := 1000;

    name_sal_table.DELETE('Gerhard');

    dbms_output.put_line(name_sal_table('Anton'));
END;
/
```
📌 `INDEX BY` erlaubt beliebige Schlüsseltypen (z. B. `VARCHAR`) statt nur Ganzzahlen.

---

## ⚡ 3. Verwendung mit BULK COLLECT
```sql
DECLARE
    TYPE DeptRecTab IS TABLE OF dept%ROWTYPE;
    dept_recs DeptRecTab;

    CURSOR c1 IS
        SELECT deptno, dname, loc FROM dept WHERE deptno > 10;
BEGIN
    OPEN c1;
    FETCH c1 BULK COLLECT INTO dept_recs;
    CLOSE c1;

    FOR i IN 1 .. dept_recs.COUNT LOOP
        dbms_output.put_line(
            'Abt: ' || dept_recs(i).deptno ||
            ', Name: ' || dept_recs(i).dname ||
            ', Ort: ' || dept_recs(i).loc);
    END LOOP;
END;
/
```
📌 Du speicherst mehrere Zeilen auf einmal in einer Tabelle von Records.

---

## 🧾 Methoden für Nested Tables

| Methode        | Beschreibung                                 |
|----------------|----------------------------------------------|
| `EXTEND`       | Fügt ein Element ans Ende an                |
| `DELETE(i)`    | Löscht das Element mit Index `i`            |
| `FIRST`        | Liefert kleinsten Index                      |
| `LAST`         | Liefert größten Index                      |
| `COUNT`        | Anzahl der Einträge                         |
| `EXISTS(i)`    | Prüft, ob Index `i` vorhanden ist           |

---

## ✅ Empfehlung

| Ziel / Anwendung                         | Empfohlene Struktur         |
|------------------------------------------|-----------------------------|
| Reihenfolge wichtig (z. B. Liste)         | Nested Table (Array)        |
| Beliebige Schlüssel (z. B. Namen)         | Nested Table mit `INDEX BY` |
| Viele Zeilen aus Tabelle holen           | BULK COLLECT + Nested Table |

---

#### Stock.java
```java
package jp.shop.dto;

import java.sql.Timestamp;

public class Stock {

    private int id;
    private String item;
    private int price;
    private int quantity;
    private Timestamp updateDate;

    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getItem() {
        return item;
    }
    public void setItem(String item) {
        this.item = item;
    }
    public int getPrice() {
        return price;
    }
    public void setPrice(int price) {
        this.price = price;
    }
    public int getQuantity() {
        return quantity;
    }
    public void setQuantity(int quantity) {
        this.quantity = quantity;
    }
    public Timestamp getUpdateDate() {
        return updateDate;
    }
    public void setUpdateDate(Timestamp updateDate) {
        this.updateDate = updateDate;
    }

}
```

#### StockDAO.java
```java
package jp.shop.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

import jp.shop.dto.Stock;

public class StockDAO {

    protected Connection con;

    /**
     * コンストラクタ
     * @param con 接続情報
     */
    public StockDAO(Connection con) {
        this.con = con;
    }

    /**
     * findAllメソッド
     * @return List<Stock> 在庫情報
     */
    public List<Stock> findAll() throws SQLException {
        List<Stock> listItem = new ArrayList<>();
        String sql = "SELECT * FROM stock";

        try (PreparedStatement ps = con.prepareStatement(sql)) {

            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                Stock item = new Stock();
                item.setId(rs.getInt("id"));
                item.setItem(rs.getString("item"));
                item.setPrice(rs.getInt("price"));
                item.setQuantity(rs.getInt("quantity"));
                item.setUpdateDate(rs.getTimestamp("update_date"));
                listItem.add(item);
            }
        }
        return listItem;
    }

    /**
     * findByIdメソッド
     * @param id ID
     * @return Stock 在庫情報
     */
    public Stock findById(int id) throws SQLException {
        Stock stock = null;
        String sql = "SELECT * FROM stock WHERE id = ?";

        try (PreparedStatement ps = con.prepareStatement(sql)) {

            ps.setInt(1, id);
            ResultSet rs = ps.executeQuery();

            if (rs.next()) {
                stock = new Stock();
                stock.setId(rs.getInt("id"));
                stock.setItem(rs.getString("item"));
                stock.setPrice(rs.getInt("price"));
                stock.setQuantity(rs.getInt("quantity"));
                stock.setUpdateDate(rs.getTimestamp("update_date"));
            }

        }
        return stock;
    }

    /**
     * createメソッド
     * @param stock 在庫情報
     * @return int 登録件数
     */
    public int create(Stock stock) throws SQLException {
        String sql = "INSERT INTO stock (item, price, quantity) VALUES (?, ?, ?)";

        try (PreparedStatement ps = con.prepareStatement(sql)) {

            ps.setString(1, stock.getItem());
            ps.setInt(2, stock.getPrice());
            ps.setInt(3, stock.getQuantity());

            return ps.executeUpdate();

        }
    }

    /**
     * updateメソッド
     * @param stock 在庫情報
     * @param id 更新対象ID
     * @return int 更新件数
     */
    public int update(Stock stock, int id) throws SQLException {
        String sql = "UPDATE stock SET item = ?, price = ?, quantity = ? WHERE id = ?";

        try (PreparedStatement ps = con.prepareStatement(sql)) {

            ps.setString(1, stock.getItem());
            ps.setInt(2, stock.getPrice());
            ps.setInt(3, stock.getQuantity());
            ps.setInt(4, id);

            return ps.executeUpdate();

        }
    }

    /**
     * deleteメソッド
     * @param id 削除対象ID
     * @return int 削除件数
     */
    public int delete(int id) throws SQLException {
        String sql = "DELETE FROM stock WHERE id = ?";

        try (PreparedStatement ps = con.prepareStatement(sql)) {

            ps.setInt(1, id);
            return ps.executeUpdate();

        }
    }
}
```

#### StockMain.java
```java
package jp.shop.main;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.List;
import java.util.Scanner;

import jp.shop.dao.StockDAO;
import jp.shop.dto.Stock;

public class StockMain {

    private static StockDAO dao = null;
    private static Scanner scan = new Scanner(System.in);

    /**
     * 在庫一覧表示
     */
    static void showList() throws SQLException {

        // TODO 在庫情報を全件取得する
        List<Stock> listItem = dao.findAll();

        System.out.println("【在庫一覧】");
        System.out.println(String.format("%3s", "ID") + " | " +
                String.format("%-20s", "ITEM") + " | " +
                String.format("%8s", "PRICE") + " | " +
                String.format("%10s", "QUANTITY") + " |");
        System.out.println("----------------------------------------------------");
        for (Stock item : listItem) {
            System.out.println(String.format("%3s", item.getId()) + " | " +
                    String.format("%-20s", item.getItem()) + " | " +
                    String.format("%8s", item.getPrice()) + " | " +
                    String.format("%10s", item.getQuantity()) + " |");
        }
    }

    /**
     * 在庫登録処理
     */
    static void create() throws SQLException {
        System.out.println();
        System.out.println("【在庫登録】");
        System.out.println("------ 登録情報を入力してください ------");

        System.out.print("商品名：");
        String item = scan.next();
        System.out.print("価格：");
        int price = scan.nextInt();
        System.out.print("数量：");
        int quantity = scan.nextInt();

        // TODO Stockクラスのインスタンスを生成し、登録内容をセットする
        Stock stock = new Stock();
        stock.setItem(item);
        stock.setPrice(price);
        stock.setQuantity(quantity);

        /*
         * TODO
         * StockDAOのinsertメソッドを実行する
         * 成功時：「登録が完了しました」
         * 失敗時：「登録に失敗しました」
         */
        if (dao.create(stock) > 0) {
            System.out.println("\r\n登録が完了しました");
        } else {
            System.out.println("\r\n登録に失敗しました");
        }

        System.out.println("----------------------------------------");
        System.out.println();
    }

    /**
     * 在庫更新処理
     */
    static void update() throws SQLException {
        System.out.println();
        System.out.println("【在庫更新】");
        System.out.println("------ 更新するIDを入力してください ------");

        System.out.print("ID：");
        int id = scan.nextInt();

        /*
         * TODO
         * 指定したIDが存在するかチェックする
         * 存在しない場合はメッセージを表示して更新処理を終了する
         * 「指定したIDの在庫は見つかりません」
         */
        Stock oldStock = dao.findById(id);
        if (oldStock == null) {
            System.out.println("\r\n指定したIDの在庫は見つかりません");
            System.out.println("------------------------------------------");
            System.out.println();
            return;
        }

        System.out.println();
        System.out.println("------ 更新内容を入力してください --------");
        System.out.print("商品名：");
        String item = scan.next();
        System.out.print("価格：");
        int price = scan.nextInt();
        System.out.print("数量：");
        int quantity = scan.nextInt();

        // TODO Stockクラスのインスタンスを生成し、更新内容をセットする
        Stock newStock = new Stock();
        newStock.setItem(item);
        newStock.setPrice(price);
        newStock.setQuantity(quantity);

        /*
         * TODO
         * StockDAOのupdateメソッドを実行する
         * 成功時：「更新が完了しました」
         * 失敗時：「更新に失敗しました」
         */
        if (dao.update(newStock, id) > 0) {
            System.out.println("\r\n更新が完了しました");
        } else {
            System.out.println("\r\n更新に失敗しました");
        }
        System.out.println("------------------------------------------");
        System.out.println();
    }

    /**
     * 在庫削除処理
     */
    static void delete() throws SQLException {
        System.out.println();
        System.out.println("【在庫削除】");
        System.out.println("------ 削除するIDを入力してください ------");

        System.out.print("ID：");
        int id = scan.nextInt();

        /*
         * TODO
         * 指定したIDが存在するかチェックする
         * 存在しない場合はメッセージを表示して更新処理を終了する
         * 「指定したIDの在庫は見つかりません」
         */
        Stock oldStock = dao.findById(id);
        if (oldStock == null) {
            System.out.println("\r\n指定したIDの在庫は見つかりません");
            System.out.println("------------------------------------------");
            System.out.println();
            return;
        }

        /*
         * TODO
         * StockDAOのupdateメソッドを実行する
         * 成功時：「削除が完了しました」
         * 失敗時：「削除に失敗しました」
         */
        if (dao.delete(id) > 0) {
            System.out.println("\r\n削除が完了しました");
        } else {
            System.out.println("\r\n削除に失敗しました");
        }
        System.out.println("------------------------------------------");
        System.out.println();
    }

    public static void main(String[] args) {

        final String URL = "jdbc:mysql://localhost:3306/sample_shop?useSSL=false";
        final String USER = "root";
        final String PASSWORD = "";    // TODO パスワードの設定

        try (Connection con = DriverManager.getConnection(URL, USER, PASSWORD)) {

            loop:
            while (true) {
                dao = new StockDAO(con);

                // 在庫一覧表示
                showList();

                System.out.println("\r\n*** 処理番号を選択 ***");
                System.out.println("1:登録　2:更新　3:削除　4:終了");
                int select = scan.nextInt();

                if (select < 1 || select > 4) {
                    System.out.println("\r\n1～4の処理番号を入力してください");
                    continue;
                }

                switch (select) {
                case 1:
                    // 在庫登録処理
                    create();
                    break;
                case 2:
                    // 在庫更新処理
                    update();
                    break;
                case 3:
                    // 在庫削除処理
                    delete();
                    break;
                case 4:
                    System.out.println("\r\nプログラムを終了します。");
                    break loop;
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```
